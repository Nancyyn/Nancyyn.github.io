---
title: JS 五
date: 2019-11-03 15:04:50
categories:
  - 面试
tags:
  - Js
  - 面试
---

**JS 面试总结分为五部分，本节为第五部分：**

### 11.promise 特点、优缺点、手动实现 Promise

#### 11.1 Promise 基本特性

- Promise 有三种状态：pending（进行中）、fulfilled（已成功）、rejected（已失败）。

- Promise 对象接受一个回调函数作为参数，该回调函数接受两个参数，分别是成功时的回调 resolve 和失败时的回调 reject；另外 resolve 的参数除了正常值以外，还可能是一个 Promise 对象的实例；reject 的参数通常是一个 error 对象的实例。

- then 方法返回一个新的 Promise 实例，并接收两个参数 onResolved（fulfilled 状态的回调）；onRejected（rejected 状态的回调，该参数可选）。

- catch 方法返回一个新的 Promise 实例。

- Promise.all()方法将多个 Promise 实例包装成一个新的 Promise 实例，该方法接受一个由 Promise 对象组成的数组作为参数，注意参数中只要有一个实例触发 canth 方法，都会触发 Promise.all()方法返回的新的实例的 catch 方法，如果参数中的某个实例本身调用了 catch 方法，将不会触发 Promise.all()方法返回的新实例的 catch 方法。

- Promise.race()方法的参数与 Promise.all()方法一样，参数中的实例只要有一个率先改变状态就会将该实例的状态传给 Promise.race()方法，并将返回值作为 Promise.race()方法产生的 Promise 实例的返回值。

<!--more-->

#### 11.2 Promise 优点

- 统一异步 API

  Promise 的一个重要优点是它逐渐被用作浏览器的异步 API，统一现在各种各样的 API，以及不兼容的模式和手法。

- Promise 与事件对比

  和事件相比较，Promise 更适合处理一次性的结果。在结果计算出来之前之后注册回调函数都是可以的，都可以拿到正确的值。Promise 的这个优点很自然。但是，不能使用 Promise 处理多次触发的事件。链式处理是 Promise 的又一优点，但是事件却不能这样链式处理。

- Promise 与回调对比

  解决了回调地狱的问题，将异步操作以同步操作的流程表达出来。

- Promise 带来的额外好处是包含了更好的错误处理方式（包含了异常处理），并且写起来很轻松，因为可以重用一些同步的工具，比如：Array.prototype.map()。

#### 11.3 Promise 缺点

- 无法取消 Promise，一旦新建，它就会立即执行，无法中途取消。

- 如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。

- 当处于 Pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

- Promise 真正执行回调的时候，定义 Promise 那部分实际上已经走完了，所以 Promise 的报错堆栈上下文不太友好。

#### 11.4 手动实现 Promise

```javascript
function myPromise(constructor) {
  let self = this
  self.status = 'pending' //定义状态改变前的初始状态
  self.value = undefined //定义状态为resolved的时候的状态
  self.reason = undefined //定义状态为rejected的时候的状态
  function resolve(value) {
    //两个==="pending"，保证了了状态的改变是不不可逆的
    if (self.status === 'pending') {
      self.value = value
      self.status = 'resolved'
    }
  }
  function reject(reason) {
    //两个==="pending"，保证了了状态的改变是不不可逆的
    if (self.status === 'pending') {
      self.reason = reason
      self.status = 'rejected'
    }
  }
  //捕获构造异常
  try {
    constructor(resolve, reject)
  } catch (e) {
    reject(e)
  }
}
myPromise.prototype.then = function (onFullfilled, onRejected) {
  let self = this
  switch (self.status) {
    case 'resolved':
      onFullfilled(self.value)
      break
    case 'rejected':
      onRejected(self.reason)
      break
    default:
  }
}

// 测试
var p = new myPromise(function (resolve, reject) {
  resolve(1)
})
p.then(function (x) {
  console.log(x)
})
//输出1
```

#### 11.5 手动实现 Promise.all

```javascript
function promiseAll(promises) {
  return new Promise(function (resolve, reject) {
    if (!Array.isArray(promises)) {
      throw new TypeError(`argument must be a array`)
    }
    var resolvedCounter = 0
    var promiseNum = promises.length
    var resolvedResult = []
    for (let i = 0; i < promiseNum; i++) {
      Promise.resolve(promises[i]).then(
        (value) => {
          resolvedCounter++
          resolvedResult[i] = value
          if (resolvedCounter == promiseNum) {
            return resolve(resolvedResult)
          }
        },
        (error) => {
          return reject(error)
        }
      )
    }
  })
}

// test
let p1 = new Promise(function (resolve, reject) {
  setTimeout(function () {
    resolve(1)
  }, 1000)
})
let p2 = new Promise(function (resolve, reject) {
  setTimeout(function () {
    resolve(2)
  }, 2000)
})
let p3 = new Promise(function (resolve, reject) {
  setTimeout(function () {
    resolve(3)
  }, 3000)
})
promiseAll([p3, p1, p2]).then((res) => {
  console.log(res) // [3, 1, 2]
})
```

### 12.页面优化方案

#### 12.1. 软件优化

- **html 优化** 优化布局，减少空格，不用 table 布局，尽量不用 iframe 标签

- **css 优化** 可以用 css 实现的尽量不用 js 实现；css 代码压缩；css 合并；用字体图标代替图片；开启 css 硬件加速；transform 动画由 GPU 控制，支持硬件加速，并不需要软件方面的渲染，使用 top 和 left 实现动画时浏览器发生的重绘

- **js 优化** 图片优化（预加载/懒加载/延时加载）；视频或音频不加载，当点击之后开始单独加载视频或音频；在 js 中尽量减少闭包的使用（原因：闭包会产生不释放的栈内存）

- **http 优化** 尽量减少需要发送的 http 请求

- **缓存方面** 使用浏览器的缓存机制，不需要每次登录或者怎么样都需要再去访问服务器；利用浏览器和服务器端的缓存技术（304 缓存），把一些不经常更新的静态资源文件做缓存处理

#### 12.2 硬件优化

- **设置负载均衡服务器** 通过负载均衡服务器使得后台数据压力平衡

- **增加带宽** 但是硬件的成本远远高于软件优化

### 13. async 与 await

任意一个名称都是有意义的，先从字面意思来理解。async 是“异步”的简写，而 await 可以认为是 async wait 的简写。所以应该很好理解 async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成。

#### 13.1 async

**async 函数返回的是一个 Promise 对象**， 如果在函数中 `return` 一个直接量，async 会把这个直接量通过 `Promise.resolve()` 封装成 Promise 对象。

![async](async.png)

在最外层不能用 await 获取其返回值的情况下，我们当然应该用原来的方式：`then()` 链来处理这个 Promise 对象，就像这样

```javascript
testAsync().then((v) => {
  console.log(v) // 输出 hello async
})
```

#### 13.2 await

因为 async 函数返回一个 Promise 对象，所以 await 可以用于等待一个 async 函数的返回值——这也可以说是 await 在等 async 函数，但要清楚，**它等的实际是一个返回值**。注意到 await 不仅仅用于等 Promise 对象，它可以等任意表达式的结果，所以，await 后面实际是可以接普通函数调用或者直接量的。

如果它等到的`不是一个 Promise 对象`，那 await 表达式的运算结果就是它等到的东西。

如果它等到的是一个 Promise 对象，await 就忙起来了，它会`阻塞`后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。

```javascript
function fn() {
  return new Promise((resolve) => {
    console.log(1)
  })
}
async function f1() {
  await fn()
  console.log(2)
}
f1()
console.log(3)
//1
//3
```

这个代码因为 fn 是属于同步的，所以先打印出 1，然后是 3，但是因为没有 resolve 结果，所以 await 拿不到值，因此不会打印 2

```javascript
function fn() {
  return new Promise((resolve) => {
    console.log(1)
    resolve()
  })
}
async function f1() {
  await fn()
  console.log(2)
}
f1()
console.log(3)
//1
//3
//2
```

这个代码与前面相比多了个 resolve 说明 promise 成功了，所以 await 能拿到结果，因此就是 1 3 2
