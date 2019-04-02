# ES 6 （let & const ）

## let

### 不存在变量提升

```javascript
console.log(foo); //undefined
var foo = 1;

console.log(box); //Uncaught ReferenceError: box is not defined

let box = "box";
```

### 暂时性死区（TDZ）

只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

下例中，box 在块级作用域中 被 let 重新声明，但是又在声明前 被赋值了。 所以会报错。

总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

```javascript
var box = "out";
{
  box = "in";
  let box;
  console.log(box); // ncaught ReferenceError: box is not defined
}
```

所以当我们用 typeof 去检测一个变量的时候 如果 变量 未声明，也会报错

```javascript
console.log(typeof a); //undefined
var a = 1;

console.log(typeof b); //b is not defined
let b = 2;
```

### for 循环中 块级作用域

`for`循环还有一个特别之处，就是设**置循环变量的那部分**是一个父作用域，而**循环体内**部是一个单独的子作用域。

```javascript
for (let i = 1; i < 10; i++) {
  let i = "box";
  console.log(i);
}
// "box" * 9
```

### 不允许重复声明

```javascript
let a = 2;

let a = "str";
console.log(a); //Identifier 'a' has already been declared
```

```javascript
function box(a) {
  let a = 1;
}
box(); //Identifier 'a' has already been declared
```

## 块级作用域

### 块级作用域 与 函数声明

ES6 规定，块级作用域之中，函数声明语句的行为类似于 let，在块级作用域之外不可引用。

块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE）不再必要了。

```javascript
{
  let a = 1;
  console.log(a + 1); //2
}
//等效
(function() {
  var a = 1;
  console.log(a + 1); //2
})();
```

ES6 规定，块级作用域之中，函数声明语句的行为类似于 let，在块级作用域之外不可引用。

下面两种不同的函数声明方式 导致不同结果。
由于 第一种 是 函数表达式，
第二种是函数正式声明相当于 var 这样 就直接导致 函数不被块级作用域所限制，升级到了最高作用域。

```javascript
{
  let box = function() {
    console.log("box");
  };

  box(); //box
}

box(); //box is not defined

{
  function box() {
    console.log("box");
  }
}

box(); //box
```

## const

**`const`声明一个只读的常量。一旦声明，常量的值就不能改变。**

**`const`的作用域与`let`命令相同：只在声明所在的块级作用域内有效。**

**`const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。**
