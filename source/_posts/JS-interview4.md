---
title: JS 四
date: 2019-11-03 14:59:50
categories:
  - 面试
tags:
  - Js
  - 面试
---

**JS 面试总结分为五部分，本节为第四部分：**

### 9.类数组和数组的区别，dom 的类数组如何转换为数组

#### 9.1 定义

**数组是一个特殊对象，与常规对象的区别：**

- 当有新元素添加到列表中时，自动更新 length 属性；

- 设置 length 属性，可以截断数组；

- 从 Array.prototype 中继承了方法；

- 属性为'Array'。
- **类数组是一个拥有 length 属性，并且它属性为非负整数的普通对象，类数组不能直接调用数组方法。**

<!--more-->

#### 9.2.区别

**本质：类数组是简单对象，它的原型关系与数组不同**

```javascript
// 原型关系和原始值转换
let arrayLike = {
  length: 10
}
console.log(arrayLike instanceof Array) // false
console.log(arrayLike.__proto__.constructor === Array) // false
console.log(arrayLike.toString()) // [object Object]
console.log(arrayLike.valueOf()) // {length: 10}

let array = []
console.log(array instanceof Array) // true
console.log(array.__proto__.constructor === Array) // true
console.log(array.toString()) // ''
console.log(array.valueOf()) // []
```

#### 9.3 类数组转换为数组

**转换方法**

- 使用 Array.from()

- 使用 Array.prototype.slice.call()

- 使用 Array.prototype.forEach()进行属性遍历并组成新的数组
  **转换须知**
  转换后的数组长度由 length 属性决定，索引不连续时转换结果是连续的，会自动补位

```javascript
let al1 = {
  length: 4,
  0: 0,
  1: 1,
  3: 3,
  4: 4,
  5: 5
}
console.log(Array.from(al1)) // [0, 1, undefined, 3]
```

- 仅考虑 0 或正整数的索引

```javascript
// 代码示例
let al2 = {
  length: 4,
  '-1': -1,
  0: 0,
  a: 'a',
  1: 1
}
console.log(Array.from(al2)) // [0, 1, undefined, undefined]
```

- 使用 slice 转换产生稀疏数组

```javascript
// 代码示例
let al2 = {
  length: 4,
  '-1': -1,
  0: 0,
  a: 'a',
  1: 1
}
console.log(Array.prototype.slice.call(al2)) //[0, 1, empty × 2]
```

#### 9.4 使用数组方法操作类数组注意地方

```javascript
let arrayLike2 = {
  2: 3,
  3: 4,
  length: 2,
  push: Array.prototype.push
}

// push 操作的是索引值为 length 的位置
arrayLike2.push(1)
console.log(arrayLike2) // {2: 1, 3: 4, length: 3, push: ƒ}
arrayLike2.push(2)
console.log(arrayLike2) // {2: 1, 3: 2, length: 4, push: ƒ}
```

### 10.浏览器事件循环机制

#### 10.1 为什么会有 Event Loop

JaveScript 的**任务**分为两种**同步**和**异步**，它们的处理方式也各自不同，**同步任务**是直接放在主线程上排队依次执行，**异步任务**会放在任务队列中，若有多个异步任务则需要在任务队列中排队等待，任务队列类似于缓冲区，任务下一步会被移到**调用栈**，然后主线程执行调用栈的任务。

> **调用栈**：调用栈是一个栈结构，函数调用会形成一个栈帧，帧中包含了当前执行函数的参数和局部变量等上下文信息，函数执行完后，它的执行上下文会从栈中弹出。

JavaScript 是**单线程**的，单线程是指 js 引擎中解析和执行 js 代码的线程只有一个（主线程），每次只能做一件事情，然而 ajax 请求中，主线程在等待相应的过程中会去做其他事情，浏览器先在事件表注册 ajax 的回调函数，响应回来后回调函数被添加到任务队列中等待执行，不会造成线程阻塞，所以说 js 处理 ajax 请求的方式是异步的。

综上所述，检查调用栈是否为空以及将某个任务添加到调用栈中的过程就是 Event Loop，这就是 javaScript 实现异步的核心。

#### 10.2 浏览器中的 Event Loop

##### 10.2.1 Micro-Task（微任务）和 Macro-Task（宏任务）

- 浏览器端事件循环中的异步队列有两种：macro（宏任务）队列和 micro（微任务）队列。

- 常见的 macro-task：setTimeout、setInterval、script（整体代码）、I/O 操作、UI 渲染等。

- 常见的 micro-task：new Promise().then(回调)、MutationObserve 等。

##### 10.2.2 requestAnimationFrame

requestAnimationFrame 也属于异步执行的方法，但该方法既不属于宏任务，也不属于微任务，按照 MDN 中的定义：

> window.requestAnimationFrame()告诉浏览器-----你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。
> requestAnimationFrame 是 GUI 渲染之前执行，但在 Micro-Task 之后，不过 requestAnimationFrame 不一定会在当前帧必须执行，由浏览器根据当前的策略自行决定在哪一帧执行。

##### 10.2.3 async/await

- async
  当我们在函数前使用 async 的时候，使得该函数返回的是一个 Promise 对象

```javascript
async function test() {
  return 1 // async的函数会在这里帮我们隐式使用Promise.resolve(1)
}
// 等价于下面的代码
function test() {
  return new Promise(function (resolve, reject) {
    resolve(1)
  })
}
```

可见 async 只是一个语法糖，只是帮助我们返回一个 Promise 而已

- await
  await 表示等待，是右侧「表达式」的结果，这个表达式的计算结果可以是 Promise 对象的值或者一个函数的值（换句话说，就是没有特殊限定）。并且只能在带有 async 的内部使用

使用 await 时，会从右往左执行，当遇到 await 时，会阻塞函数内部处于它后面的代码，去执行该函数外部的同步代码，当外部同步代码执行完毕，再回到该函数内部执行剩余的代码, 并且当 await 执行完毕之后，会先处理微任务队列的代码

下面来看一个栗子:

```javascript
async function async1() {
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2')
}
console.log('script start')
setTimeout(function () {
  console.log('setTimeout')
}, 0)
async1()
new Promise(function (resolve) {
  console.log('promise1')
  resolve()
}).then(function () {
  console.log('promise2')
})
console.log('script end')
```

下面是在 chrome 浏览器上输出的结果

```javascript
script start
async1 start
async2
promise1
script end
promise2
async1 end
undefined
setTimeout
```

使用事件循环机制分析:

- 首先执行同步代码，console.log( 'script start' )
- 遇到 setTimeout,会被推入宏任务队列
- 执行 async1(), 它也是同步的，只是返回值是 Promise，在内部首先执行 console.log( 'async1 start' )
- 然后执行 async2(), 然后会打印 console.log( 'async2' )
- 从右到左会执行, 当遇到 await 的时候，阻塞后面的代码，去外部执行同步代码
- 进入 new Promise,打印 console.log( 'promise1' )
- 将.then 放入事件循环的微任务队列
- 继续执行，打印 console.log( 'script end' )
- 外部同步代码执行完毕，接着回到 async1()内部, 由于 async2()其实是返回一个 Promise, await async2()相当于获取它的值，其实就相当于这段代码 Promise.resolve(undefined).then((undefined) => {}),所以.then 会被推入微任务队列, 所以现在微任务队列会有两个任务。接下来处理微任务队列，打印 console.log( 'promise2' )，后面一个.then 不会有任何打印，但是会执行
- 执行后面的代码, 打印 console.log( 'async1 end' )
- 进入第二次事件循环，执行宏任务队列, 打印 console.log( 'setTimeout' )

##### 10.2.4 event loop 过程

> 当某个宏任务执行完后，会查看是否有微任务队列。如果有，先执行微任务队列中的所有任务，如果没有，会读取宏任务队列中排在最前的任务，执行宏任务的过程中，遇到微任务，依次加入微任务队列。栈空后，再次读取微任务队列里的任务，依次类推。
