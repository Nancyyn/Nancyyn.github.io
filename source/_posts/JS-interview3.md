---
title: JS 三
date: 2019-11-03 14:55:50
categories:
  - 面试
tags:
  - Js
  - 面试
---

**JS 面试总结分为五部分，本节为第三部分：**

### 6.介绍防抖节流原理、区别以及应用，并用 JavaScript 进行实现

#### 6.1 防抖

**原理**：在事件被触发 n 秒后再执行回调，如果在这 n 秒内又被触发，则重新计时。
**使用场景**：

- 按钮提交场景：防止多次提交按钮，只执行最后提交的一次。

- 搜索框联想场景：防止联想发送请求，只发送最后一次输入（用户在不断输入值时，用防抖来节约请求资源）。

**简易版实现**

```javascript
function debounce(func, wait) {
  let timeout
  return function () {
    const context = this
    const args = arguments
    clearTimeout(timeout)
    timeout = setTimeout(function () {
      func.apply(context, args)
    }, wait)
  }
}
```

<!--more-->

**立即执行版实现**

> 有时希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。

```javascript
// 有时希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。
function debounce(func, wait, immediate) {
  let timeout
  return function () {
    const context = this
    const args = arguments
    if (timeout) clearTimeout(timeout)
    if (immediate) {
      const callNow = !timeout
      timeout = setTimeout(function () {
        timeout = null
      }, wait)
      if (callNow) func.apply(context, args)
    } else {
      timeout = setTimeout(function () {
        func.apply(context, args)
      }, wait)
    }
  }
}
```

**返回值版实现**

> func 函数可能会有返回值，所以需要返回函数结果，但是当 immediate 为 false 的时候，因为使用了 setTimeout，我们将 func.apply(context,args)的返回值赋给变量，最后再 return 的时候，值将会一直是 undefined，所以只在 immediate 为 true 的时候返回函数的执行结果。

```javascript
function debounce(func, wait, immediate) {
  let timeout, result
  return function () {
    const context = this
    const args = arguments
    if (timeout) clearTimeout(timeout)
    if (immediate) {
      const callNow = !timeout
      timeout = setTimeout(function () {
        timeout = null
      }, wait)
      if (callNow) result = func.apply(context, args)
    } else {
      timeout = setTimeout(function () {
        func.apply(context, args)
      }, wait)
    }
    return result
  }
}
```

#### 6.2 节流

**原理**：规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。
**适用场景**：

- 拖拽场景：固定时间内只执行一次，防止超高频次触发位置变动。

- 缩放场景：监控浏览器 resize。

**使用时间戳实现**

> 使用时间戳，当触发事件的时候，我们取出当前的时间戳，然后减去之前的时间戳（最一开始值设为 0），如果大于设置的时间周期，就执行函数，然后更新时间戳为当前的时间戳，如果小于，就不执行。

```javascript
function throttle(func, wait) {
  let context, args
  let previous = 0

  return function () {
    let now = +new Date()
    context = this
    args = arguments
    if (now - previous > wait) {
      func.apply(context, args)
      previous = now
    }
  }
}
```

**使用定时器实现**

> 当触发事件的时候，我们设置了一个定时器，在触发事件的时候，如果定时器存在，就不执行，知道定时器执行，然后执行函数，清空定时器，这样就可以设置下一个定时器。

```javascript
function throttle(func, wait) {
  let timeout
  return function () {
    const context = this
    const args = arguments
    if (!timeout) {
      timeout = setTimeout(function () {
        timeout = null
        func.apply(context, args)
      }, wait)
    }
  }
}
```

#### 6.3 总结 (简要答案)

- 防抖：防止抖动，单位时间内事件触发会被重置，避免事件被误伤触发多次。代码实现重在清零 clearTimeout

- 节流：控制流量，单位时间内事件只能触发一次，如果服务器端的限流即 Rate Limit。代码实现重在开锁关锁 timer=timeout; timer=null

![debounce](debounce.png)

### 7.对闭包的看法，为什么要用闭包？说一下闭包原理以及应用场景

#### 7.1 什么是闭包

函数执行后返回结果是一个内部函数，并被外部变量所引用，如果内部函数持有被执行函数作用域的变量，即形成了**闭包**。

可以在内部函数访问到外部函数作用域。使用闭包，一可以读取函数中的变量，二可以将函数中的变量存储在内存中，保护变量不被污染。而正因为闭包会把函数中的变量存储在内存中，会对内存有消耗，所以不能滥用闭包，否则会影响网页性能，造成内存泄漏。当不需要使用闭包时，要及时释放内存，可将内层函数对象的变量赋值为 null。

#### 7.2 闭包原理

函数执行分成两个阶段（预编译阶段和执行阶段）

- **预编译阶段**

> 在预编译阶段，如果发现内部函数使用了外部函数的变量，则会在内存中创建一个“闭包”对象并保存对应变量值，如果已存在“闭包”，则只需要增加对应属性值即可。
> **执行阶段**
> 执行完后，函数执行上下文会被销毁，函数对“闭包”对象的引用也会被销毁，但其内部函数还持用该“闭包”的引用，所以内部函数可以继续使用“外部函数”中的变量。

利用了函数作用域链的特性，一个函数内部定义的函数会将包含外部函数的活动对象添加到它的作用域链中，函数执行完毕，其执行作用域链销毁，但因内部函数的作用域链仍然在引用这个活动对象，所以其活动对象不会被销毁，直到内部函数被销毁后才被销毁。

#### 7.3 优点

- 可以从内部函数访问外部函数的作用域中的变量，且访问到的变量长期驻扎在内存中，可供之后使用。
- 避免变量污染全局。
- 把变量存到独立的作用域，作为私有成员存在。

#### 7.4 缺点

- 对内存消耗有负面影响，因内部函数保存了对外部变量的引用，导致无法被垃圾回收，增大内存使用量，所以使用不当会导致内存泄漏。
- 对处理速度具有负面影响。闭包的层级决定了引用的外部变量在查找时经过的作用域链长度。
- 可能获取到意外的值（captured vaule）。

#### 7.5 应用场景

- 典型应用是模块封装，在各模块规范出现之前，都是用这样的方式防止变量污染全局。

```javascript
var Yideng = (function () {
  // 这样声明为模块私有变量，外界无法直接访问
  var foo = 0

  function Yideng() {}
  Yideng.prototype.bar = function bar() {
    return foo
  }
  return Yideng
})()
```

- 在循环中创建闭包，防止取到意外的值。
  如下代码，无论哪个元素触发事件，都会弹出 3。因为函数执行后引用的 i 是同一个，而 i 在循环结束后就是 3。

```javascript
for (var i = 0; i < 3; i++) {
  document.getElementById('id' + i).onfocus = function () {
    alert(i)
  }
}
//可用闭包解决
function makeCallback(num) {
  return function () {
    alert(num)
  }
}
for (var i = 0; i < 3; i++) {
  document.getElementById('id' + i).onfocus = makeCallback(i)
}
```

### 8.链式调用的核心就在于调用完的方法将自身实例返回。

```javascript
//创建一个bird类
function Bird(name) {
  this.name = name
  this.run = function () {
    document.write(name + ' ' + 'start run;')
    return this // return this返回当前调用方法的对象。
  }
  this.stopRun = function () {
    document.write(name + ' ' + 'start run;')
    return this
  }
  this.sing = function () {
    document.write(name + ' ' + 'start sing;')
    return this
  }
  this.stopSing = function () {
    document.write(name + ' ' + 'start stopSing;')
    return this
  }
}
```

```javascript
var bird = new Bird('测试')
bird.run().sing().stopSing().stopRun()
```
