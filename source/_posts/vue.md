---
title: vue
date: 2020-01-08 15:55:22
categories:
  - Vue
tags:
  - Vue
  - 面试
---

### 1. 说说你对 vue 的理解？

#### 1.1 vue 是什么

Vue.js（/vjuː/，或简称为 Vue）是一个用于创建用户界面的开源 JavaScript 框架，也是一个创建单页应用的 Web 应用框架。

#### 1.2 vue 核心特性

##### 1.2.1 数据驱动（MVVM)

```
MVVM`表示的是 `Model-View-ViewModel
```

- Model：模型层，负责处理业务逻辑以及和服务器端进行交互
- View：视图层：负责将数据模型转化为 UI 展示出来，可以简单的理解为 HTML 页面
- ViewModel：视图模型层，用来连接 Model 和 View，是 Model 和 View 之间的通信桥梁

<!--more-->

##### 1.2.2 组件化

1.什么是组件化一句话来说就是把图形、非图形的各种逻辑均抽象为一个统一的概念（组件）来实现开发的模式，在`Vue`中每一个`.vue`文件都可以视为一个组件 2.组件化的优势

- 降低整个系统的耦合度，在保持接口不变的情况下，我们可以替换不同的组件快速完成需求，例如输入框，可以替换为日历、时间、范围等组件作具体的实现
- 调试方便，由于整个系统是通过组件组合起来的，在出现问题的时候，可以用排除法直接移除组件，或者根据报错的组件快速定位问题，之所以能够快速定位，是因为每个组件之间低耦合，职责单一，所以逻辑会比分析整个系统要简单
- 提高可维护性，由于每个组件的职责单一，并且组件在系统中是被复用的，所以对代码进行优化可获得系统的整体升级

##### 1.2.3 指令系统

解释：指令 (Directives) 是带有 v- 前缀的特殊属性作用：当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM

- 常用的指令
  - 条件渲染指令 `v-if`
  - 列表渲染指令`v-for`
  - 属性绑定指令`v-bind`
  - 事件绑定指令`v-on`
  - 双向数据绑定指令`v-model`

没有指令之前我们是怎么做的？是不是先要获取到 DOM 然后在....干点啥

#### 1.3 Vue 和 React 对比

##### 相同点

- 都有组件化思想
- 都支持服务器端渲染
- 都有 Virtual DOM（虚拟 dom）
- 数据驱动视图
- 都有支持 native 的方案：`Vue`的`weex`、`React`的`React native`
- 都有自己的构建工具：`Vue`的`vue-cli`、`React`的`Create React App`

##### 区别

- 数据变化的实现原理不同。`react`使用的是不可变数据，而`Vue`使用的是可变的数据
- 组件化通信的不同。`react`中我们通过使用回调函数来进行通信的，而`Vue`中子组件向父组件传递消息有两种方式：事件和回调函数
- diff 算法不同。`react`主要使用 diff 队列保存需要更新哪些 DOM，得到 patch 树，再统一操作批量更新 DOM。`Vue` 使用双向指针，边对比，边更新 DOM

### 2. 说说你对双向绑定的理解？

#### 2.1 双向绑定的原理是什么？

我们都知道 `Vue` 是数据双向绑定的框架，双向绑定由三个重要部分构成

- 数据层（Model）：应用的数据及业务逻辑
- 视图层（View）：应用的展示效果，各类 UI 组件
- 业务逻辑层（ViewModel）：框架封装的核心，它负责将数据与视图关联起来

而上面的这个分层的架构方案，可以用一个专业术语进行称呼：`MVVM`这里的控制层的核心功能便是 “数据双向绑定” 。自然，我们只需弄懂它是什么，便可以进一步了解数据绑定的原理

**理解 ViewModel**

它的主要职责就是：

- 数据变化后更新视图
- 视图变化后更新数据

当然，它还有两个主要部分组成

- 监听器（Observer）：对所有数据的属性进行监听
- 解析器（Compiler）：对每个元素节点的指令进行扫描跟解析,根据指令模板替换数据,以及绑定相应的更新函数

#### 2.2 实现双向绑定

我们还是以`Vue`为例，先来看看`Vue`中的双向绑定流程是什么的

1. `new Vue()`首先执行初始化，对`data`执行响应化处理，这个过程发生`Observe`中
2. 同时对模板执行编译，找到其中动态绑定的数据，从`data`中获取并初始化视图，这个过程发生在`Compile`中
3. 同时定义⼀个更新函数和`Watcher`，将来对应数据变化时`Watcher`会调用更新函数
4. 由于`data`的某个`key`在⼀个视图中可能出现多次，所以每个`key`都需要⼀个管家`Dep`来管理多个`Watcher`
5. 将来 data 中数据⼀旦发生变化，会首先找到对应的`Dep`，通知所有`Watcher`执行更新函数

流程图如下：

![Observer](Observer.png)

##### 2.2.1 实现

先来一个构造函数：执行初始化，对`data`执行响应化处理

```javascript
class Vue {
  constructor(options) {
    this.$options = options
    this.$data = options.data

    // 对data选项做响应式处理
    observe(this.$data)

    // 代理data到vm上
    proxy(this)

    // 执行编译
    new Compile(options.el, this)
  }
}
```

对`data`选项执行响应化具体操作

```javascript
function observe(obj) {
  if (typeof obj !== 'object' || obj == null) {
    return
  }
  new Observer(obj)
}

class Observer {
  constructor(value) {
    this.value = value
    this.walk(value)
  }
  walk(obj) {
    Object.keys(obj).forEach((key) => {
      defineReactive(obj, key, obj[key])
    })
  }
}
```

###### 2.2.1.1 编译`Compile`

对每个元素节点的指令进行扫描跟解析,根据指令模板替换数据,以及绑定相应的更新函数

![Compile](Compile.png)

```javascript
class Compile {
  constructor(el, vm) {
    this.$vm = vm
    this.$el = document.querySelector(el) // 获取dom
    if (this.$el) {
      this.compile(this.$el)
    }
  }
  compile(el) {
    const childNodes = el.childNodes
    Array.from(childNodes).forEach((node) => {
      // 遍历子元素
      if (this.isElement(node)) {
        // 判断是否为节点
        console.log('编译元素' + node.nodeName)
      } else if (this.isInterpolation(node)) {
        console.log('编译插值⽂本' + node.textContent) // 判断是否为插值文本 {{}}
      }
      if (node.childNodes && node.childNodes.length > 0) {
        // 判断是否有子元素
        this.compile(node) // 对子元素进行递归遍历
      }
    })
  }
  isElement(node) {
    return node.nodeType == 1
  }
  isInterpolation(node) {
    return node.nodeType == 3 && /\{\{(.*)\}\}/.test(node.textContent)
  }
}
```

###### 2.2.1.2 依赖收集

视图中会用到`data`中某`key`，这称为依赖。同⼀个`key`可能出现多次，每次都需要收集出来用⼀个`Watcher`来维护它们，此过程称为依赖收集多个`Watcher`需要⼀个`Dep`来管理，需要更新时由`Dep`统⼀通知

![Watcher](Watcher.png)

实现思路

1. `defineReactive`时为每⼀个`key`创建⼀个`Dep`实例
2. 初始化视图时读取某个`key`，例如`name1`，创建⼀个`watcher1`
3. 由于触发`name1`的`getter`方法，便将`watcher1`添加到`name1`对应的 Dep 中
4. 当`name1`更新，`setter`触发时，便可通过对应`Dep`通知其管理所有`Watcher`更新

```javascript
// 负责更新视图
class Watcher {
  constructor(vm, key, updater) {
    this.vm = vm
    this.key = key
    this.updaterFn = updater

    // 创建实例时，把当前实例指定到Dep.target静态属性上
    Dep.target = this
    // 读一下key，触发get
    vm[key]
    // 置空
    Dep.target = null
  }

  // 未来执行dom更新函数，由dep调用的
  update() {
    this.updaterFn.call(this.vm, this.vm[this.key])
  }
}
```

声明`Dep`

```javascript
class Dep {
  constructor() {
    this.deps = [] // 依赖管理
  }
  addDep(dep) {
    this.deps.push(dep)
  }
  notify() {
    this.deps.forEach((dep) => dep.update())
  }
}
```

创建`watcher`时触发`getter`

```javascript
class Watcher {
  constructor(vm, key, updateFn) {
    Dep.target = this
    this.vm[this.key]
    Dep.target = null
  }
}
```

依赖收集，创建`Dep`实例

```javascript
jfunction defineReactive(obj, key, val) {
  this.observe(val);
  const dep = new Dep();
  Object.defineProperty(obj, key, {
    get() {
      Dep.target && dep.addDep(Dep.target);// Dep.target也就是Watcher实例
      return val;
    },
    set(newVal) {
      if (newVal === val) return;
      dep.notify(); // 通知dep执行更新方法
    },
  });
}
```
