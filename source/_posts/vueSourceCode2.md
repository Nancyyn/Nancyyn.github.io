---
title: vue2源码之虚拟DOM篇
date: 2020-03-10 15:55:22
categories:
  - Vue
tags:
  - Vue
  - 技术总结
---

**vue2 源码 总结分为四部分，本节为第二部分：**

### 2. 虚拟 DOM 篇

#### 2.1 Vue 中的虚拟 DOM

##### 2.1.1 什么是虚拟 DOM?

所谓虚拟 DOM，就是用一个`JS`对象来描述一个`DOM`节点，像如下示例：

```javascript
<div class="a" id="b">我是内容</div>

{
  tag:'div',        // 元素标签
  attrs:{           // 属性
    class:'a',
    id:'b'
  },
  text:'我是内容',  // 文本内容
  children:[]       // 子元素
}
```

我们把组成一个`DOM`节点的必要东西通过一个`JS`对象表示出来，那么这个`JS`对象就可以用来描述这个`DOM`节点，我们把这个`JS`对象就称为是这个真实`DOM`节点的虚拟`DOM`节点。

<!--more-->

##### 2.1.2 为什么要有虚拟 DOM?

我们知道，`Vue`是数据驱动视图的，数据发生变化视图就要随之更新，在更新视图的时候难免要操作`DOM`,而操作真实`DOM`又是非常耗费性能的，这是因为浏览器的标准就把 `DOM` 设计的非常复杂，所以一个真正的 `DOM` 元素是非常庞大的，如下所示：

```javascript
let div = document.createElement('div')
let str = ''
for (const key in div) {
  str += key + ''
}
console.log(str)
```

![divDOM](divDOM.png)

上图中我们打印一个简单的空`div`标签，就打印出这么多东西，更不用说复杂的、深嵌套的`DOM`节点了。由此可见，直接操作真实`DOM`是非常消耗性能的。

那么有没有什么解决方案呢？当然是有的。我们可以用`JS`的计算性能来换取操作`DOM`所消耗的性能。

既然我们逃不掉操作`DOM`这道坎,但是我们可以尽可能少的操作`DOM`。那如何在更新视图的时候尽可能少的操作`DOM`呢？最直观的思路就是我们不要盲目的去更新视图，而是通过对比数据变化前后的状态，计算出视图中哪些地方需要更新，只更新需要更新的地方，而不需要更新的地方则不需关心，这样我们就可以尽可能少的操作`DOM`了。这也就是上面所说的用`JS`的计算性能来换取操作`DOM`的性能。

我们可以用`JS`模拟出一个`DOM`节点，称之为虚拟`DOM`节点。当数据发生变化时，我们对比变化前后的虚拟`DOM`节点，通过`DOM-Diff`算法计算出需要更新的地方，然后去更新需要更新的视图。

这就是虚拟`DOM`产生的原因以及最大的用途。

##### 2.2.3 Vue 中的虚拟 DOM

###### 2.2.3.1 VNode 类

我们说了，虚拟`DOM`就是用`JS`来描述一个真实的`DOM`节点。而在`Vue`中就存在了一个`VNode`类，通过这个类，我们就可以实例化出不同类型的虚拟`DOM`节点，源码如下：

```javascript
// 源码位置：src/core/vdom/vnode.js

export default class VNode {
  constructor(
    tag?: string,
    data?: VNodeData,
    children?: ?Array<VNode>,
    text?: string,
    elm?: Node,
    context?: Component,
    componentOptions?: VNodeComponentOptions,
    asyncFactory?: Function
  ) {
    this.tag = tag /*当前节点的标签名*/
    this.data =
      data /*当前节点对应的对象，包含了具体的一些数据信息，是一个VNodeData类型，可以参考VNodeData类型中的数据信息*/
    this.children = children /*当前节点的子节点，是一个数组*/
    this.text = text /*当前节点的文本*/
    this.elm = elm /*当前虚拟节点对应的真实dom节点*/
    this.ns = undefined /*当前节点的名字空间*/
    this.context = context /*当前组件节点对应的Vue实例*/
    this.fnContext = undefined /*函数式组件对应的Vue实例*/
    this.fnOptions = undefined
    this.fnScopeId = undefined
    this.key = data && data.key /*节点的key属性，被当作节点的标志，用以优化*/
    this.componentOptions = componentOptions /*组件的option选项*/
    this.componentInstance = undefined /*当前节点对应的组件的实例*/
    this.parent = undefined /*当前节点的父节点*/
    this.raw = false /*简而言之就是是否为原生HTML或只是普通文本，innerHTML的时候为true，textContent的时候为false*/
    this.isStatic = false /*静态节点标志*/
    this.isRootInsert = true /*是否作为跟节点插入*/
    this.isComment = false /*是否为注释节点*/
    this.isCloned = false /*是否为克隆节点*/
    this.isOnce = false /*是否有v-once指令*/
    this.asyncFactory = asyncFactory
    this.asyncMeta = undefined
    this.isAsyncPlaceholder = false
  }

  get child(): Component | void {
    return this.componentInstance
  }
}
```

从上面的代码中可以看出：`VNode`类中包含了描述一个真实`DOM`节点所需要的一系列属性，如`tag`表示节点的标签名，`text`表示节点中包含的文本，`children`表示该节点包含的子节点等。通过属性之间不同的搭配，就可以描述出各种类型的真实`DOM`节点。

###### 2.2.3.2 VNode 的类型

- 注释节点
- 文本节点
- 元素节点
- 组件节点
- 函数式组件节点
- 克隆节点

###### 2.2.3.3 VNode 的作用

说了这么多，那么`VNode`在`Vue`的整个虚拟`DOM`过程起了什么作用呢？

其实`VNode`的作用是相当大的。我们在视图渲染之前，把写好的`template`模板先编译成`VNode`并缓存下来，等到数据发生变化页面需要重新渲染的时候，我们把数据发生变化后生成的`VNode`与前一次缓存下来的`VNode`进行对比，找出差异，然后有差异的`VNode`对应的真实`DOM`节点就是需要重新渲染的节点，最后根据有差异的`VNode`创建出真实的`DOM`节点再插入到视图中，最终完成一次视图更新。

##### 2.1.4 总结

本章首先介绍了虚拟`DOM`的一些基本概念和为什么要有虚拟`DOM`，其实说白了就是以`JS`的计算性能来换取操作真实`DOM`所消耗的性能。接着从源码角度我们知道了在`Vue`中是通过`VNode`类来实例化出不同类型的虚拟`DOM`节点，并且学习了不同类型节点生成的属性的不同，所谓不同类型的节点其本质还是一样的，都是`VNode`类的实例，只是在实例化时传入的属性参数不同罢了。最后探究了`VNode`的作用，有了数据变化前后的`VNode`，我们才能进行后续的`DOM-Diff`找出差异，最终做到只更新有差异的视图，从而达到尽可能少的操作真实`DOM`的目的，以节省性能。

> 很多人认为虚拟 DOM 最大的优势是 diff 算法，减少 JavaScript 操作真实 DOM 的带来的性能消耗。虽然这一个虚拟 DOM 带来的一个优势，但并不是全部。`虚拟 DOM 最大的优势在于抽象了原本的渲染过程，实现了跨平台的能力`，而不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件，可以是近期很火热的小程序，也可以是各种 GUI

#### 2.2 Vue 中的 DOM-Diff

##### 2.2.1 前言

在上一篇文章介绍`VNode`的时候我们说了，`VNode`最大的用途就是在数据变化前后生成真实`DOM`对应的虚拟`DOM`节点，然后就可以对比新旧两份`VNode`，找出差异所在，然后更新有差异的`DOM`节点，最终达到以最少操作真实`DOM`更新视图的目的。而对比新旧两份`VNode`并找出差异的过程就是所谓的`DOM-Diff`过程。`DOM-Diff`算法是整个虚拟`DOM`的核心所在

##### 2.2.2 patch

在`Vue`中，把 `DOM-Diff`过程叫做`patch`过程。patch,意为“补丁”，即指对旧的`VNode`修补，打补丁从而得到新的`VNode`，非常形象哈。那不管叫什么，其本质都是把对比新旧两份`VNode`的过程。我们在下面研究`patch`过程的时候，一定把握住这样一个思想：所谓旧的`VNode`(即`oldVNode`)就是数据变化之前视图所对应的虚拟`DOM`节点，而新的`VNode`是数据变化之后将要渲染的新的视图所对应的虚拟`DOM`节点，所以我们要以生成的新的`VNode`为基准，对比旧的`oldVNode`，如果新的`VNode`上有的节点而旧的`oldVNode`上没有，那么就在旧的`oldVNode`上加上去；如果新的`VNode`上没有的节点而旧的`oldVNode`上有，那么就在旧的`oldVNode`上去掉；如果某些节点在新的`VNode`和旧的`oldVNode`上都有，那么就以新的`VNode`为准，更新旧的`oldVNode`，从而让新旧`VNode`相同。

可能你感觉有点绕，没关系，我们在说的通俗一点，你可以这样理解：假设你电脑上现在有一份旧的电子版文档，此时老板又给了你一份新的纸质板文档，并告诉你这两份文档内容大部分都是一样的，让你以新的纸质版文档为准，把纸质版文档做一份新的电子版文档发给老板。对于这个任务此时，你应该有两种解决方案：一种方案是不管它旧的文档内容是什么样的，统统删掉，然后对着新的纸质版文档一个字一个字的敲进去，这种方案就是不用费脑，就是受点累也能解决问题。而另外一种方案是以新的纸质版文档为基准，对比看旧的电子版文档跟新的纸质版文档有什么差异，如果某些部分在新的文档里有而旧的文档里没有，那就在旧的文档里面把这些部分加上；如果某些部分在新的文档里没有而旧的文档里有，那就在旧的文档里把这些部分删掉；如果某些部分在新旧文档里都有，那就对比看有没有需要更新的，最后在旧的文档里更新一下，最终达到把旧的文档变成跟手里纸质版文档一样，完美解决。

对比以上两种方案，显然你和`Vue`一样聪明，肯定会选择第二种方案。第二种方案里的旧的电子版文档对应就是已经渲染在视图上的`oldVNode`，新的纸质版文档对应的是将要渲染在视图上的新的`VNode`。总之一句话：**以新的 VNode 为基准，改造旧的 oldVNode 使之成为跟新的 VNode 一样，这就是 patch 过程要干的事**。

说了这么多，听起来感觉好像很复杂的样子，其实不然，我们仔细想想，整个`patch`无非就是干三件事：

- 创建节点：新的`VNode`中有而旧的`oldVNode`中没有，就在旧的`oldVNode`中创建。
- 删除节点：新的`VNode`中没有而旧的`oldVNode`中有，就从旧的`oldVNode`中删除。
- 更新节点：新的`VNode`和旧的`oldVNode`中都有，就以新的`VNode`为准，更新旧的`oldVNode`。

###### 2.2.2.1 创建节点

![createNode](createNode.png)

###### 2.2.2.2 删除节点

如果某些节点再新的`VNode`中没有而在旧的`oldVNode`中有，那么就需要把这些节点从旧的`oldVNode`中删除。删除节点非常简单，只需在要删除节点的父元素上调用`removeChild`方法即可。源码如下：

```javascript
function removeNode(el) {
  const parent = nodeOps.parentNode(el) // 获取父节点
  if (isDef(parent)) {
    nodeOps.removeChild(parent, el) // 调用父节点的removeChild方法
  }
}
```

###### 2.2.2.3 更新节点

![updateNode](updateNode.png)

##### 2.2.3 总结

在本篇文章中我们介绍了`Vue`中的`DOM-Diff`算法：patch 过程。我们先介绍了算法的整个思想流程，然后通过梳理算法思想，了解了整个`patch`过程干了三件事，分别是：创建节点，删除节点，更新节点。并且对每件事情都对照源码展开了细致的学习，画出了其逻辑流程图。另外对于更新节点中，如果新旧`VNode`里都包含了子节点，我们就需要细致的去更新子节点，关于更新子节点的过程我们在下一篇文章中展开学习。

#### 2.3 更新子节点

创建并插入子节点： **合适的位置是所有未处理节点之前，而并非所有已处理节点之后**。

#### 2.4 优化更新子节点

`Vue`为了避免双重循环数据量大时间复杂度升高带来的性能问题，而选择了从子节点数组中的 4 个特殊位置互相比对，分别是：新前与旧前，新后与旧后，新后与旧前，新前与旧后。
