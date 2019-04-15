# Generator 函数

## 基本语法

### 函数定义规则&特性

**函数规则**

1.function 与函数名 之间 带有 **\***

2.函数体内部使用 yield 表达式，定义不同的内部状态

**函数特性**

调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象

```javascript
function* logOut() {
  yield "hello";
  yield "this is the second string";
  return "ending";
}
let logout = logOut();
console.log(logout.next()); // { value: 'hello', done: false }
console.log(logout.next()); // { value: 'this is the second string', done: false }
console.log(logout.next()); // { value: 'ending', done: true }
console.log(logout.next()); // { value: undefined, done: true }
```

上面就是一个简单的 genertator 函数。

**遍历器对象的 next 方法的运行逻辑如下。**

（1）遇到 yield 表达式，就暂停执行后面的操作，并将紧跟在 yield 后面的那个表达式的值，作为返回的对象的 value 属性值。

（2）下一次调用 next 方法时，再继续往下执行，直到遇到下一个 yield 表达式。

（3）如果没有再遇到新的 yield 表达式，就一直运行到函数结束，直到 return 语句为止，并将 return 语句后面的表达式的值，作为返回的对象的 value 属性值。

（4）如果该函数没有 return 语句，则返回的对象的 value 属性值为 undefined。

### yield 表达式

**惰性求值**

**yield 表达式不可再普通函数中使用**

**yield 表达式在其他表达式中 必须放在圆括号里。**

```javascript
function* genFn1() {
  yield 12 * 2;
  console.log("hello" + (yield "world"));
}
let gen = genFn1();
console.log(gen.next()); //{ value: 24, done: false }
console.log(gen.next()); //{ value: undefined, done: true }
```

**yield 作为函数参数放在赋值表达式右边 可以不加圆括号**

```javascript
function box(a, b) {
  console.log(a, b);
}
function* genBox() {
  box(yield "A", "B");
}
```

### for ... of 循环

**for...of 循环可以自动遍历 Generator 函数运行时生成的 Iterator 对象，且此时不再需要调用 next 方法。**

```javascript
function* genCount() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}
let count = genCount();
for (let i of count) {
  console.log(i); // 1 2 3 4 5
}
```

一旦 next 方法的返回对象的 done 属性为 true，for...of 循环就会中止，且不包含该返回对象，所以上面代码的 return 语句返回的 6

除了 for...of 循环以外，
**扩展运算符(...)**、**解构赋值**和**Array.from**方法内部调用的，都是遍历器接口。
这意味着，它们都可以将 Generator 函数返回的 Iterator 对象，作为参数。

```javascript
function* numbers() {
  yield 1;
  yield 2;
  return 3;
  yield 4;
}
```

```javascript
// 扩展运算符
[...numbers()]; // [1, 2]

Array.from 方法
Array.from(numbers()); // [1, 2]

// 解构赋值
let [x, y] = numbers();
x; // 1
y; // 2

// for...of 循环
for (let n of numbers()) {
console.log(n);
}
// 1
// 2
```

### Generator.prototype.throw()

Generator 函数返回的遍历器对象，都有一个 throw 方法，可以在函数体外抛出错误，然后在 Generator 函数体内捕获。

**throw 接受一个参数，参数会被 Catch 接收**

```javascript
var genFn5 = function*() {
  try {
    yield;
  } catch (e) {
    console.log("内部捕获", e);
  }
};
var gen5 = genFn5();
gen5.next();
try {
  gen5.throw("a"); // 内部捕获 a
  gen5.throw("b"); // 外部捕获 b
  // gen5.next();
} catch (e) {
  console.log("外部捕获" + e);
}
```

上面我们在 genFn5 外抛出了两个错误。

抛出 a 的时候，被 genFn5 内的 catch()捕获

抛出 b 的时候 ，由于 genFn5 内的 Catch 已经执行过。不会再捕捉这个错误了。

所以又被抛出到了体外，被外部的 catch 捕获。

**全局的 throw 和 generator.throw()不要混淆**

```javascript
function* genFn6() {
  try {
    yield;
  } catch (e) {
    if (e != "a") {
      console.log("内部捕获" + e);
    }
  }
}
let gen6 = genFn6();
gen6.next();
gen6.throw("the error was throwed by gen6"); //内部捕获the error was throwed by gen6

try {
  throw "throw outside";
} catch (e) {
  console.log(e); //throw outside
}
```

如果 Generator **函数内部没有部署 try...catch**代码块，

那么 throw 方法抛出的错误，将被**外部 try...catch**代码块捕获。

```javascript
function* genFn7() {
  yield;
  console.log("内部捕获" + e);
}
let gen7 = genFn7();
gen7.next();
try {
  gen7.throw("gen7 is throwing a msg");
} catch (e) {
  console.log("外部捕获:" + e); //外部捕获:gen7 is throwing a msg
}
```

如果 Generator **函数内部外部都没有部署 try...catch**代码块，

程序将报错，执行

```javascript
function* genFn8() {
  yield 1;
  console.log(e);
}
let gen8 = genFn8();
gen8.next();
gen8.throw("gen8 is throwing a msg"); //generator.js:266 Uncaught gen8 is throwing a msg
// 此时 gen8抛出的错误 无法被内外捕获。
```

**generator.throw 方法被捕获以后，会附带执行下一条 yield 表达式。**

**也就是说，会附带执行一次 next 方法。**

```javascript
function* genFn9() {
  yield console.log("yield action 1");
  try {
    yield console.log("yield action 2");
  } catch (e) {
    console.log("内部捕获:" + e);
  }
  yield console.log("yield action 3");
}
let gen9 = genFn9();
gen9.next(); //yield action 1
gen9.next(); //yield action 2
gen9.throw(1); // 内部捕获:1  yield action 3
console.log(gen9.next()); //{ value: undefined, done: true }
```

上述例子中，当执行了两次 next()的时候，throw 了一个错误，并且附带执行了下一个 next()

只要 Generator 函数内部部署了 try...catch 代码块，那么遍历器的 throw 方法抛出的错误，不影响下一次遍历

需要注意的是，**当函数体内有多个 yield 和 throw 并存的时候，一定要按照顺序来**，否则 会报错。

**函数体外的 try..catch 去捕获 函数体内的的错误**

```javascript
function* genFn10() {
  let x = yield;
  let y = x.length;
  yield y;
}
let gen10 = genFn10();
gen10.next();
try {
  gen10.next();
} catch (e) {
  console.log("外部捕获到内部的错误" + e);
  //外部捕获到内部的错误TypeError: Cannot read property 'length' of undefined
}
```

上面例子中 我们在内部没有传入值进去，所以 x 为 undefined ,所以会报错。

这样做肯定错的 。/ 那么我们在 外部去 catch 这个错误。

**一旦 Generator 执行过程中抛出错误，且没有被内部捕获，就不会再执行下去了**

如果此后还调用 next 方法，将返回一个 value 属性等于 undefined、done 属性等于 true 的对象，

即 JavaScript 引擎认为这个 Generator 已经运行结束了。

### generator.return()

return 如果外部有传参数,那么返回的 value 就是传入的参数,

如果函数内部 有 return 一个值, 那返回的 value 仍是 undefined

但是 done 的值 都是 true 表示已经执行结束了.

```javascript
function* genFn11() {
  yield 1;
  let a = yield;
  return 4;
}

let gen11 = genFn11();
gen11.next();
let end = gen11.return("genFn endding");
console.log(end); //{value: "genFn endding", done: true}
```

如果 Generator 函数内部有 try...finally 代码块，且正在执行 try 代码块，

那么 return 方法会推迟到 finally 代码块执行完再执行。

```javascript
function* genFn12() {
  try {
    yield console.log(1);
    yield console.log(2);
    yield console.log(3);
  } finally {
    yield console.log(4);
    yield console.log("finally");
  }
  return;
}

let gen12 = genFn12();
gen12.next(); //1
gen12.next(); //2
gen12.return("endding "); //4 return 之前一定是要等到finally执行完后. 所以回先执行finally中的语句.
gen12.next(); // finally
console.log(gen12.next()); //{value: "endding ", done: true}
```

上面例子最后一个 next 对应就是执行完了 finally 后,

接着执行 return 了

### next()/throw()/return() 的共同点

**next()是将 yield 表达式替换成一个值。**

当 next 执行的是 return 那一步的时候 ,next()再传值 也没用了,

因为此时 generator 函数已经结束了.

但是可以 继续往下 return 改变最终的值

```javascript
const genFn13 = function*(x, y) {
  let result = yield x + y;
  yield result;
  try {
  } catch (e) {
    console.log("内部捕获" + e);
  }
  console.log(result);
  return "this is inside return value:" + result;
};

let gen13 = genFn13(1, 2);
console.log(gen13.next()); //{value: 3, done: false}
console.log(gen13.next(1)); //{value: 1, done: false}
// 相当于将 let result = yield x + y
// 替换成 let result = 1;
console.log(gen13.next(4)); //{value: "this is inside return value:1", done: true}
console.log(gen13.return(4)); //{value: 4, done: true}
console.log(gen13.return(5)); //{value: 5, done: true}
console.log(gen13.return(6)); //{value: 6, done: true}
```

yield\* 表达式

如果在 Generator 函数内部，调用另一个 Generator 函数，

默认情况下是没有效果的。

```javascript
function* genBoo() {
  yield "A";
  yield "B";
}
function* genPoo() {
  yield "X";
  genBoo();
  yield "Y";
}

let poo = genPoo();
console.log(poo.next()); //{value: "X", done: false}
console.log(poo.next()); //{value: "Y", done: false}
```

**yield\* **
表达式，用来在一个 Generator 函数里面执行另一个 Generator 函数。

```javascript
function* genBoo() {
  yield "A";
  yield "B";
}
function* genPoo() {
  yield "X";
  yield* genBoo();
  yield "Y";
}
let poo = genPoo();
console.log(poo.next()); //{value: "X", done: false}
console.log(poo.next()); //{value: "A", done: false}
console.log(poo.next()); //{value: "B", done: false}
console.log(poo.next()); //{value: "Y", done: false}
```

如果不加\*号,我们来看看返回的结果

```javascript
function* genQoo() {
  yield "M";
  yield genBoo();
  yield "N";
}

let qoo = genQoo();
console.log(qoo.next().value); //M
console.log(qoo.next().value); // genBoo {<suspended>}
console.log(qoo.next().value); //N
```

如果不加\*号
返回的是一个遍历器对象

**yield\* 后面的 Generator 函数（没有 return 语句时），等同于在 Generator 函数内部，部署一个 for...of 循环。**

那么就意味着

任何数据结构只要有 Iterator 接口，就可以被 yield\*遍历。

```javascript
function* genFn14() {
  yield "hello";
  yield [1, 2, 3, 4, 5];
  yield* "hello";
  yield* [1, 2, 3, 4, 5];
}
let gen14 = genFn14();
console.log(gen14.next().value); //hello
console.log(gen14.next().value); // [1, 2, 3, 4, 5]
console.log(gen14.next().value); // h
console.log(gen14.next().value); // e
console.log(gen14.next().value); // l
for (let i of genFn14()) {
  console.log(i);
}
```

**如果被代理的 Generator 函数有 return 语句，那么就可以向代理它的 Generator 函数返回数据。**

```javascript
function* genFn15() {
  yield 1;
  yield 2;
  return 3;
}
function* genFn15do() {
  yield "first";
  let result = yield* genFn15();
  console.log(result);
  yield "endding";
}
let gen15 = genFn15do();
console.log(gen15.next()); // {value: "first", done: false}
console.log(gen15.next()); // {value: 1, done: false}
console.log(gen15.next()); // {value: 2, done: false}
console.log(gen15.next()); // 3 , {value: "endding", done: false}
console.log(gen15.next()); //{value: undefined, done: true}
console.log(...genFn15do()); // first 1 2 endding
```

如上面看到的 genFn15() 最后返回 3 赋给了 result

我们在用`...`遍历的是,发现 return 的值 是不计在内的.

这里要注意.

关于写法问题

```javascript
function* test() {
  let re = yield 1;
  re = yield 2;
  re = yield 3;
  return re;
}
let tt = test();
console.log(...test()); // 1 2 3
console.log(tt.next()); //{value: 1, done: false}
console.log(tt.next()); //{value: 2, done: false}
console.log(tt.next()); //{value: 3, done: false}
console.log(test().next()); //{value: 1, done: false}
console.log(test().next()); //{value: 1, done: false}
console.log(test().next()); //{value: 1, done: false}
```

如果我们直接采用 **函数().next()** 去写,

在函数内部 是不会继续向下执行的.

应该正确按照 **变量.next()** 的格式去写

### 作为对象属性的 generator 函数

```javascript
let obj = {
  *genFn() {
    yield 1;
    yield 2;
    return 3;
  },
  // 完整写法

  genFn2: function*() {
    yield 1;
    yield 2;
    return 3;
  }
};
let objgen1 = obj.genFn();
let objgen2 = obj.genFn2();
console.log(objgen1.next());
console.log(objgen2.next());
```

### generator 的 this 属性

Generator 函数总是返回一个遍历器，

ES6 规定这个遍历器是 Generator 函数的实例，

也继承了 Generator 函数的 prototype 对象上的方法。

```javascript
function* genFn16() {
  this.name = "michele";
}
genFn16.prototype.say = function() {
  console.log("hello");
};
let gen16 = genFn16();
gen16.next();
console.log(gen16.name); //undefined
console.log(gen16.say()); //hello
```

可以看到 如果在 generator 中定义的属性 是无法直接获取到的.
并且 generator 是不能用 new 去实例化的.

**解决办法**

```javascript
function* genFunc() {
  this.a = "a";
  yield (this.b = "b");
  yield (this.c = "c");
}

let obj = {};
let f = genFunc.call(obj);

console.log(f.next()); //{value: "b", done: false}
console.log(f.next()); //{value: "c", done: false}
console.log(obj.a);
```

上面 我们将 genFunc 内部的 this 绑定了 obj,
执行完之后, 内部属性定义完成.
这时，所有内部属性都绑定在 obj 对象上了，因此 obj 对象也就成了 genFunc 的实例。

上面代码中，执行的是遍历器对象 f，但是生成的对象实例是 obj，有没有办法将这两个对象统一呢？

只要我们将 genFunc 的 this 指向它的 prototype,

那么里面定义的属性 就直接挂在了 prototype 上.

```javascript
function* genFunc() {
  this.a = "a";
  yield (this.b = "b");
  yield (this.c = "c");
}
let x = genFunc();
let f = genFunc.call(genFunc.prototype);
console.log(genFunc.prototype);

console.log(f.next()); //{value: "b", done: false}
console.log(f.next()); //{value: "c", done: false}
console.log(f.a); // a
console.log(f.b); //b
console.log(genFunc.prototype);
```

如果想要使用 new 去构造实例
**可以将它改成构造函数**

```javascript
function* genFunc() {
  this.a = "a";
  yield (this.b = "b");
  yield (this.c = "c");
}
let GenF = function() {
  return genFunc.call(genFunc.prototype);
};

let g1 = new GenF();
console.log(g1.next()); //{value: "b", done: false}
console.log(g1.next()); //{value: "c", done: false}
console.log(g1.a); //a
console.log(g1.b); //b
```
