---
title: vue2源码之变化侦测篇
date: 2020-03-08 15:55:22
categories:
  - Vue
tags:
  - Vue
  - 技术总结
---

**vue2 源码 总结分为四部分，本节为第一部分：**

### 1. 变化侦测篇

#### 1.1 前言

- Vue 最大的特点之一就是数据驱动视图，我们可以把数据理解为状态，而视图就是用户可直观看到页面。

> UI = render(state)
>
> 上述公式中：状态 state 是输入，页面 UI 输出，状态输入一旦变化了，页面输出也随之而变化。我们把这种特性称之为数据驱动视图。
>
> 变化侦测就是追踪状态，亦或者说是数据的变化，一旦发生了变化，就要去更新视图。
>
> 变化侦测可不是个新名词，它在目前的前端三大框架中均有涉及。在 Angular 中是通过脏值检查流程来实现变化侦测；在 React 是通过对比虚拟 DOM 来实现变化侦测，而在 Vue 中也有自己的一套变化侦测实现机制

<!--more-->

#### 1.2 Object 的变化侦测

##### 1.2.1 使 Object 数据变得“可观测”，Object.defineProperty，getter/setter

```javascript
// 源码位置：src/core/observer/index.js

/**
 * Observer类会通过递归的方式把一个对象的所有属性都转化成可观测对象
 */
export class Observer {
  constructor(value) {
    this.value = value
    // 给value新增一个__ob__属性，值为该value的Observer实例
    // 相当于为value打上标记，表示它已经被转化成响应式了，避免重复操作
    def(value, '__ob__', this)
    if (Array.isArray(value)) {
      // 当value为数组时的逻辑
      // ...
    } else {
      this.walk(value)
    }
  }

  walk(obj: Object) {
    const keys = Object.keys(obj)
    for (let i = 0; i < keys.length; i++) {
      defineReactive(obj, keys[i])
    }
  }
}

/**
 * 使一个对象转化成可观测对象
 * @param { Object } obj 对象
 * @param { String } key 对象的key
 * @param { Any } val 对象的某个key的值
 */
function defineReactive(obj, key, val) {
  if (arguments.length === 2) {
    val = obj[key]
  }
  if (typeof val === 'object') {
    new Observer(val)
  }
  const dep = new Dep() //实例化一个依赖管理器，生成一个依赖管理数组dep
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get() {
      dep.depend() // 在getter中收集依赖
      return val
    },
    set(newVal) {
      if (val === newVal) {
        return
      }
      val = newVal
      dep.notify() // 在setter中通知依赖更新
    }
  })
}
```

##### 1.2.2 依赖收集 Dep

我们给每个数据都建一个依赖管理器 Dep(因为一个数据可能被多处使用)，谁依赖了这个数据(即谁用到了这个数据)我们就把谁放入这个依赖管理器中，那么当这个数据发生变化的时候，我们就去它对应的依赖管理器中，把每个依赖都通知一遍，告诉他们："你们依赖的数据变啦，你们该更新啦！"

> 在 getter 中收集依赖，在 setter 中通知依赖更新

```javascript
// 源码位置：src/core/observer/dep.js
export default class Dep {
  constructor() {
    this.subs = []
  }

  addSub(sub) {
    this.subs.push(sub)
  }
  // 删除一个依赖
  removeSub(sub) {
    remove(this.subs, sub)
  }
  // 添加一个依赖
  depend() {
    if (window.target) {
      this.addSub(window.target)
    }
  }
  // 通知所有依赖更新
  notify() {
    const subs = this.subs.slice()
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update()
    }
  }
}

/**
 * Remove an item from an array
 */
export function remove(arr, item) {
  if (arr.length) {
    const index = arr.indexOf(item)
    if (index > -1) {
      return arr.splice(index, 1)
    }
  }
}
```

有了依赖管理器后，我们就可以在 getter 中收集依赖，在 setter 中通知依赖更新了，代码如下：

```javascript
function defineReactive(obj, key, val) {
  if (arguments.length === 2) {
    val = obj[key]
  }
  if (typeof val === 'object') {
    new Observer(val)
  }
  const dep = new Dep() //实例化一个依赖管理器，生成一个依赖管理数组dep
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get() {
      dep.depend() // 在getter中收集依赖
      return val
    },
    set(newVal) {
      if (val === newVal) {
        return
      }
      val = newVal
      dep.notify() // 在setter中通知依赖更新
    }
  })
}
```

##### 1.2.3 依赖到底是谁？Watcher 类

其实在 Vue 中还实现了一个叫做 Watcher 的类，而 Watcher 类的实例就是我们上面所说的那个"谁"。换句话说就是：谁用到了数据，谁就是依赖，我们就为谁创建一个 Watcher 实例。在之后数据变化时，我们不直接去通知依赖更新，而是通知依赖对应的 Watch 实例，由 Watcher 实例去通知真正的视图。

Watcher 类的具体实现如下：

```javascript
export default class Watcher {
  constructor(vm, expOrFn, cb) {
    this.vm = vm
    this.cb = cb
    this.getter = parsePath(expOrFn)
    this.value = this.get()
  }
  get() {
    window.target = this
    const vm = this.vm
    let value = this.getter.call(vm, vm)
    window.target = undefined
    return value
  }
  update() {
    const oldValue = this.value
    this.value = this.get()
    this.cb.call(this.vm, this.value, oldValue)
  }
}

/**
 * Parse simple path.
 * 把一个形如'data.a.b.c'的字符串路径所表示的值，从真实的data对象中取出来
 * 例如：
 * data = {a:{b:{c:2}}}
 * parsePath('a.b.c')(data)  // 2
 */
const bailRE = /[^\w.$]/
export function parsePath(path) {
  if (bailRE.test(path)) {
    return
  }
  const segments = path.split('.')
  return function (obj) {
    for (let i = 0; i < segments.length; i++) {
      if (!obj) return
      obj = obj[segments[i]]
    }
    return obj
  }
}
```

- 下面我们分析 Watcher 类的代码实现逻辑：

当实例化 Watcher 类时，会先执行其构造函数；
在构造函数中调用了 this.get()实例方法；
在 get()方法中，首先通过 window.target = this 把实例自身赋给了全局的一个唯一对象 window.target 上，然后通过 let value = this.getter.call(vm, vm)获取一下被依赖的数据，获取被依赖数据的目的是触发该数据上面的 getter，上文我们说过，在 getter 里会调用 dep.depend()收集依赖，而在 dep.depend()中取到挂载 window.target 上的值并将其存入依赖数组中，在 get()方法最后将 window.target 释放掉。
而当数据变化时，会触发数据的 setter，在 setter 中调用了 dep.notify()方法，在 dep.notify()方法中，遍历所有依赖(即 watcher 实例)，执行依赖的 update()方法，也就是 Watcher 类中的 update()实例方法，在 update()方法中调用数据变化的更新回调函数，从而更新视图。
简单总结一下就是：Watcher 先把自己设置到全局唯一的指定位置（window.target），然后读取数据。因为读取了数据，所以会触发这个数据的 getter。接着，在 getter 中就会从全局唯一的那个位置读取当前正在读取数据的 Watcher，并把这个 watcher 收集到 Dep 中去。收集好之后，当数据发生变化时，会向 Dep 中的每个 Watcher 发送通知。通过这样的方式，Watcher 可以主动去订阅任意一个数据的变化。 为了便于理解，我们画出了其关系流程图，如下图：

![Watcher](Watcher.jpg)

##### 1.2.4 不足之处

虽然我们通过 Object.defineProperty 方法实现了对 object 数据的可观测，但是这个方法仅仅只能观测到 object 数据的取值及设置值，当我们向 object 数据里添加一对新的 key/value 或删除一对已有的 key/value 时，它是无法观测到的，导致当我们对 object 数据添加或删除值时，无法通知依赖，无法驱动视图进行响应式更新。

当然，Vue 也注意到了这一点，为了解决这一问题，Vue 增加了两个全局 API:Vue.set 和 Vue.delete。

##### 1.2.5 总结

- 首先，我们通过 Object.defineProperty 方法实现了对 object 数据的可观测，并且封装了 Observer 类，让我们能够方便的把 object 数据中的所有属性（包括子属性）都转换成 getter/seter 的形式来侦测变化。

- 接着，我们学习了什么是依赖收集？并且知道了在 getter 中收集依赖，在 setter 中通知依赖更新，以及封装了依赖管理器 Dep，用于存储收集到的依赖。

- 最后，我们为每一个依赖都创建了一个 Watcher 实例，当数据发生变化时，通知 Watcher 实例，由 Watcher 实例去做真实的更新操作。

其整个流程大致如下：

Data 通过 observer 转换成了 getter/setter 的形式来追踪变化。
当外界通过 Watcher 读取数据时，会触发 getter 从而将 Watcher 添加到依赖中。
当数据发生了变化时，会触发 setter，从而向 Dep 中的依赖（即 Watcher）发送通知。
Watcher 接收到通知后，会向外界发送通知，变化通知到外界后可能会触发视图更新，也有可能触发用户的某个回调函数等。

#### 1.3 Array 的变化侦测

##### 1.3.1 使 Array 型数据可观测

- **思路分析**

```javascript
let arr = [1, 2, 3]
arr.push(4)
Array.prototype.newPush = function (val) {
  console.log('arr被修改了')
  this.push(val)
}
arr.newPush(4)
```

在上面这个例子中，我们针对数组的原生`push`方法定义个一个新的`newPush`方法，这个`newPush`方法内部调用了原生`push`方法，这样就保证了新的`newPush`方法跟原生`push`方法具有相同的功能，而且我们还可以在新的`newPush`方法内部干一些别的事情，比如通知变化。

是不是很巧妙？`Vue`内部就是这么干的。

- **数组方法拦截器**

在`Vue`中创建了一个数组方法拦截器，它拦截在数组实例与`Array.prototype`之间，在拦截器内重写了操作数组的一些方法，当数组实例使用操作数组方法时，其实使用的是拦截器中重写的方法，而不再使用`Array.prototype`上的原生方法。如下图所示：

![Array](Array.png)

经过整理，`Array`原型中可以改变数组自身内容的方法有 7 个，分别是：`push`,`pop`,`shift`,`unshift`,`splice`,`sort`,`reverse`

- **不足之处**

前文中我们说过，对于数组变化侦测是通过拦截器实现的，也就是说只要是通过数组原型上的方法对数组进行操作就都可以侦测到，但是别忘了，我们在日常开发中，还可以通过数组的下标来操作数据，如下：

```javascript
let arr = [1, 2, 3]
arr[0] = 5 // 通过数组下标修改数组中的数据
arr.length = 0 // 通过修改数组长度清空数组
```

而使用上述例子中的操作方式来修改数组是无法侦测到的。 同样，`Vue`也注意到了这个问题， 为了解决这一问题，`Vue`增加了两个全局 API:`Vue.set`和`Vue.delete`

- **总结**

在本篇文章中，首先我们分析了对于`Array`型数据也在`getter`中进行依赖收集；其次我们发现，当数组数据被访问时我们轻而易举可以知道，但是被修改时我们却很难知道，为了解决这一问题，我们创建了数组方法拦截器，从而成功的将数组数据变的可观测。接着我们对数组的依赖收集及数据变化如何通知依赖进行了深入分析；最后我们发现`Vue`不但对数组自身进行了变化侦测，还对数组中的每一个元素以及新增的元素都进行了变化侦测，我们也分析了其实现原理。

以上就是对`Array`型数据的变化侦测分析。
