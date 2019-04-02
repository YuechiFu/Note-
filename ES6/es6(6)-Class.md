# Class

## Class 的基本用法和特性

### 基本语法

我们首先看 用传统的**构造函数去** 创建一个对象.

```javascript
function Goods(name, price, type) {
  this.name = name;
  this.price = price;
  this.type = type;
}
Goods.prototype.getTotal = function() {
  return `${this.name}      ${this.price}  `;
};
let goods1 = new Goods("ysl 圆管46", 299, "beauty");
console.log(goods1.getTotal()); //ysl 圆管46      299
```

我们再来看看 如何用 **class** 定义一个类

```javascript
class Goods {
  constructor(name, price, type) {
    this.name = name;
    this.price = price;
    this.type = type;
  }
  getTotal() {
    return `${this.name}      ${this.price}  `;
  }
}

let goods2 = new Goods("ysl 圆管46", 299, "beauty");
console.log(goods2.__proto__);
console.log(goods2.constructor); //Goods
console.log(goods2.getTotal);
```

这样 我们就定义了一个类.

可以发现 其实 class 的实例化 也是 New 一个实例.

这么看来,其实 class 一个"类",这个类的真正类型仍然是一个函数

```javascript
console.log(typeof Goods); //function
console.log(Goods === Goods.prototype.constructor); //true
```

我们知道 一个构造函数的 prototype 由 constructor 和 其他属性组成.

那么 也就意味着,class 下的其他属性,都属于 prototype 中的属性.

```javascript
console.log(goods2.getTotal === Goods.prototype.getTotal); //true
```

### 不可枚举性

我们知道 es5 中,我们给对象定义一个属性 或是 在 prototype 上添加一个属性.
默认是可枚举的

但是 在 class 中,直接在 class 下添加的属性 是不可枚举的

```javascript
for (let i in box) {
  console.log(i);
}
console.log("--------------");
for (let i in goods2) {
  console.log(i); // name,price,type,goods
}

console.log("getTotal" in goods2);
console.log("text" in box);
```

上面我们利用 for in 去分别枚举

一个构造函数实例 box 和 一个 class 实例.

可以发现,传统的 实例中的属性默认都是可以枚举的

但是**class 下直接定义的 都是不可枚举**的.

### 取值函数（getter）和存值函数（setter）

```javascript
class Box2 {
  constructor() {
    this.text = "hello world";
  }
  get article() {
    console.log(`getting current article:'${this.text}'`);
    return this.text;
  }
  set article(val) {
    if (val < 15) {
      console.log(`article has been seted to :'${val}'`);
    } else {
      console.log(`The length of article is too long `);
    }
  }
}
let box2 = new Box2();
console.log(box2.article); //
box2.article = "article"; //article has been seted to :'new set article'
```

### 属性表达式

类的属性名 可以采用表达式

```javascript
let _initInfo = "_initname";
class Obj1 {
  constructor() {
    this.name = "abcd";
  }
  [_initInfo]() {
    console.log(this.name);
  }
}
let obj1 = new Obj1();
obj1._initname(); // abcd
```

### class 表达式

与函数一样 我们可以用 表达式的方式去定义

```javascript
let Box3 = class Me {
  constructor(title, count) {
    this.title = title;
    this.count = count;
  }
  _printInfo() {
    console.log(this.title + this.count);
  }
  _printMsg() {
    console.log(Me.name);
  }
};
let box3 = new Box3("bilibi", 123);
box3._printInfo(); //bilibi123
box3._printMsg(); // Me
```

** Me 只能在 类以内才能用到.**

如果 在类以内 用不到 Me ,那么可以省略不写

```javascript
let Box4 = class {};
```

#### 立即执行的 Class

```javascript
let box5 = new class {
  constructor(title, count) {
    this.title = title;
    this.count = count;
  }
  _printInfo() {
    console.log(this.title + this.count);
  }
}("bilibi", 123);
console.log(box5.title); //bilibi
```

相当于实例化一个立即执行的 构造函数

### class 使用注意的地方

#### 1. 默认严格模式 不用 use strict 声明

#### 2. 不存在变量提升

```javascript
let foo = new Foo(); //error:Foo is not defined
class Foo {}
```

**为什么 class 不能变量提升?**
我们来看一个例子

已知 let 是不存在变量提升的.

如果此时 class 有变量提升.

那么 Sub 会在 Parent 之前先被声明

此时 就会报错`.Uncaught ReferenceError: Parent is not defined`

```javascript
let Parent = class {};
class Sub extends Parent {}
```

#### 3.name 属性

ES6 class 其实 是 ES5 中构造函数的一个包装,**本质仍然是个函数**,

所以 它也继承了 函数的**name**属性.

```javascript
console.log(Parent.name); //Parent
```

#### 4.tihs 指向

因为 class 中默认使用严格模式

所以 class 里某个属性中的 this 其实指向的是**undefined**

这就会造成 下面的情况

```javascript
class Box6 {
  constructor() {
    this.msg = "hello";
  }
  _reuse() {
    this._printInfo();
  }
  _printInfo() {
    console.log(this.msg);
  }
}

let box6 = new Box6();
let _use = box6._reuse;
// let { _reuse } = box6;
box6._reuse(); //hello
_use(); // '_printInfo' of undefined
```

上面就是 将 box6.\_reuse 做一次赋值 , 但当\_use 执行的时候

函数内部的 this 其实是 undefined,所以就会报错.

**解决办法**

1. 可以在 constructor 中绑定 this

```javascript
constructor() {
    this.msg = "hello";
      this._reuse = () => {
      this._printInfo();
     };
  }
```

2.利用箭头函数

```javascript
constructor() {
    this.msg = "hello";
     this._reuse = () => {
      this._printInfo();
     };
  }
```

## static 静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上 static 关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```javascript
class Box7 {
  constructor(title, msg = "default msg") {
    this.title = title;
    this.msg = msg;
  }
  getInfo() {
    console.log(this);
    console.log(`getInfo this title : ${this.title}`);
  }
  static getMsg() {
    console.log(this);
    console.log(`getMsg this msg : ${this.msg}`);
  }
  static getStaticMsg() {
    console.log(`this is a static msg`);
  }
  getStaticMsg() {
    console.log(`this is a  common msg from getStaticMsg`);
  }
}

let box7 = new Box7("hello Box7", "new msg from Box7");
box7.getInfo();
// Box7 {title: "hello Box7", msg: "new msg from Box7"}
//getInfo this title : hello Box7
box7.getMsg(); //box7.getMsg is not a function
Box7.getMsg.call(box7);
Box7.getStaticMsg(); //class Box7
box7.getStaticMsg(); //this is a  common msg from getStaticMsg
```

上面可以看出

#### 1.static 关键字声明的方法是不能被实例继承的.

#### 2.只能用 Box7 本身去调用,如果 一定要用实例调用可以重新 call/apply

#### 3.利用 static 关键字声明的方法调用的时候 this 指向的是 Box7 这个类.而普通方法 被实例调用时候 指向的是 实例 box7

#### 4.静态方法 可以 和普通方法 重名

如果我们通过 box7 的结构就能发现,static 的方法 其实是被写再 box7.**proto**.constructor 也就是 Box7 中.
而其他的非静态方法 实在 Box7.prototype 中.

#### 5.类继承了父类的 static 方法

```javascript
class SubBox extends Box7 {
  constructor(title, msg) {
    super(title, msg);
  }
  static getSuperMsg() {
    return super.getStaticMsg();
  }
}
let sub_box = new SubBox("subBox title", "subBox msg");
SubBox.getStaticMsg(); // this is a static msg 子类继承了父类的static 方法
SubBox.getSuperMsg(); //this is a static msg
```

## 静态属性

静态属性指的是 Class 本身的属性，即 Class.propName，而不是定义在实例对象（this）上的属性。

```javascript
class Box8 {
  constructor() {
    this.title = "Box8 title";
  }
  static count = 0; //新写法
}
Box8.staticTitle = "Box8 static title"; //老写法
let box8 = new Box8();
console.log(box8.staticTitle); //undefined
console.log(box8.count); //undefined
console.log(Box8.staticTitle); //Box8 static title
console.log(Box8.count); //0
```

## 实例属性的新写法

我们可以直接再 class 最顶层声明一个实例属性
**一定得再最顶层!!**

```javascript
class Box9 {
  count = 0;
  title = " box9 title";
  constructor() {}
}
let box9 = new Box9("box9 title");
console.log(box9.title); //box9 title
```
