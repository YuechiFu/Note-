# async 函数

# async

## async 基本用法

**定义一个 async 函数**

```javascript
async function asyFn1() {
  await console.log(1);
  await console.log(2);
  await console.log(3);
  return "全部加载完毕";
}

asyFn1().then(res => {
  console.log(res); //全部加载完毕
});
```

**async 函数返回一个 Promise 对象**

可以使用 then 方法添加回调函数。当

函数执行的时候，一旦遇到 await 就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

**如果函数内部出错了 返回 promise 对象状态变成 reject,错误会被 catch 方法捕获,整个函数到此 便中断执行**

```javascript
async function asyFn1() {
  await console.log("start");
  throw new Error("出错了");
  await console.log("endding");
}
asyFn1()
  .then()
  .catch(e => {
    console.log(e); //Error: 出错了
  });
```

我们以图片的加载为例子

如果图片加载成功 就在 页面中插入图片

如果失败就 打印出 error

```javascript
let imgList = [
  "https://www.hubsan.com/na/includes/templates/whitetemp_0517/images/lmain/F22.png"
];

function loadImg(url) {
  let Img = new Image();
  Img.onload = function() {
    console.log("加载完毕");
    return true;
  };
  Img.src = url;
}
async function asyFn2(url) {
  return await new Promise((resolve, reject) => {
    let Img = new Image();
    Img.width = 200;
    Img.onload = () => {
      console.log("image loadding successfully");
      resolve(Img);
    };
    Img.onerror = () => {
      console.log("image loadding failed");
      reject("error");
    };
    Img.src = url;
  });
}
asyFn2(imgList[6])
  .then(res => {
    console.log();
    document.querySelector(".container .list_box").appendChild(res);
  })
  .catch(e => {
    console.log(e);
  });
```

### promise 状态变化

async 函数返回的 Promise 对象，必须等到内部所有 await 命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到 return 语句或者抛出错误。也就是说，只有 async 函数内部的异步操作执行完，才会执行 then 方法指定的回调函数。

```javascript
async function asyFn4() {
  console.log("1st step action");
  await new Promise((reslove, reject) => {
    setTimeout(() => {
      console.log("此时第 1 个 await 后面的函数 已经执行完毕");
      reslove();
    }, 2500);
  });
  console.log("2nd step action");
  await new Promise((reslove, reject) => {
    setTimeout(() => {
      console.log("此时第 2 个 await 后面的函数 已经执行完毕");
      reslove();
    }, 2500);
  });
  console.log("3td step action");
  await new Promise((reslove, reject) => {
    setTimeout(() => {
      console.log("此时第 3 个 await 后面的函数 已经执行完毕");
      reslove();
    }, 2500);
  });
  return "endding";
}

asyFn4().then(res => {
  console.log(res);

  //1st step action
  //Async.js:134 此时第 1 个 await 后面的函数 已经执行完毕
  //Async.js:138 2nd step action
  //Async.js:141 此时第 2 个 await 后面的函数 已经执行完毕
  //Async.js:145 3td step action
  //Async.js:148 此时第 3 个 await 后面的函数 已经执行完毕
  //Async.js:155 endding
});
```

### await 命令

**await 命令后面是一个 Promise 对象，返回该对象的结果**

如果不是 Promise 对象，就直接返回对应的值。

```javascript
async function asyFn5() {
  return await (function() {
    console.log("abcdefg");
    return 123;
  })();
}
asyFn5().then(res => {
  console.log(res); //123
});
```

**或者是一个有定义 then 方法的对象**

```javascript
class Then {
  constructor() {
    this.time = Date.now();
  }
  then(resolve, reject) {
    resolve(this.time);
  }
}
(async () => {
  return await new Then();
})().then(res => {
  console.log(res); //1555566143090
});
```

**await 后面的 Promise 对象 如果变成了 reject**

1.  整个函数到此 便中断执行

2.  reject 的参数 会被 catch 捕获到

```javascript
async function asyFn3() {
  await console.log("start");
  await Promise.reject("error by reject");
  await console.log("endding");
}
asyFn3() // start
  .then()
  .catch(e => {
    console.log(e); //error by reject
  });
```

注意，上面代码中，await 语句前面没有 return，但是 reject 方法的参数依然传入了 catch 方法的回调函数。这里如果在 await 前面加上 return，效果是一样的。

如果我们希望即使前一个 await 发生了错误，但是后面的仍可以继续执行

1.  我们可以**把 await 放在 try...catch 　中处理**

2.  可以在 Promise 后面再跟一个 catch()

```javascript
async function asyFn6() {
  await console.log("starting!!");
  //把 await 放在 try...catch 　中处理
  try {
    await Promise.reject("error by reject");
  } catch (e) {
    console.log(e);
  }
  // Promise 后面再跟一个 catch()
  await Promise.reject("error by reject 2").catch(e => {
    console.log(e);
  });
  try {
    await consolo.log("this is consolo");
  } catch (e) {
    console.log(e);
  }
  return await "endding";
}
asyFn6().then(res => {
  console.log(res);
});
```
