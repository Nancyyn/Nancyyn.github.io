---
title: CSS 三
date: 2019-11-02 17:45:30
categories:
  - 面试
tags:
  - Css
  - 面试
---

**CSS 面试总结分为三部分，本节为第三部分：**

### 11.css 超出省略怎么写，三行超出省略怎么写

- 单行

```css
.single-ellipsis {
  width: 500px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

- 多行

```css
.multiline-ellipsis {
  display: -webkit-box;
  word-break: break-all;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3; //需要显示的行数
  overflow: hidden;
  text-overflow: ellipsis;
}
```

<!--more-->

### 12.css 如何画出一个扇形

- 简单版

```javascript
/* 简版一  */
.sector1 {
   border-radius: 0  0  0 200px;
   width: 200px;
   height: 200px;
   background: yellowgreen;
}
 /* 简版二  */
.sector1 {
  width: 0;
  height: 0;
  border-width: 100px;
  border-style: solid;
  border-color: transparent transparent red;
  border-radius: 100px;
}

```

### 13.什么是重绘重排，哪些操作会造成重绘重排

#### 13.1 什么是重绘重排

当我们改变了一个元素的尺寸位置属性时，会重新进行样式计算（computed style）布局（layout）绘制（paint）以及后面的所有流程，这种行为称为**重排**。

当改变了某个元素的颜色属性时不会重新触发布局，但还是会触发样式计算和绘制，这就是**重绘**。

我们可以发现重排和重绘都会占用主线程，还有 JS 也会运行在主线程，所有就会出现抢占执行时间的问题，如果你写了一个不断导致重排重绘的动画，浏览器则需要在每一帧都运行样式计算布局和绘制的操作。

#### 13.2 触发的一些因素

- 页面首次进入的渲染
- 浏览器 resize
- 元素位置和尺寸发生改变的时候
- 可见元素的增删
- 内容发生改变
- 字体的 font 的改变
- css 伪类激活
- ....

#### 13.3 如果优化

我们知道当前页面以每秒 60 帧的刷新率时才不会让用户感觉到页面卡顿，如果在运行动画时还有大量的 JS 任务需要执行，因为布局、绘制和 js 执行都是在主线程运行的，当在一帧的时间内布局和绘制结束后，还有剩余时间 js 就会拿到主线程的使用权，如果 js 执行时间过长，就会导致在下一帧开始时 js 没有及时归还主线程，导致下一帧动画没有按时渲染，就会出现页面的卡顿。

- **第一种优化方式：**
  requestAnimationFrame，它会在每一帧被调用，通过回调 api 的回调，可以把 js 运行任务分成一些更小的任务块，在每一帧事件用完前暂停 js 执行归还主线程，这样的话在下一帧开始时，主线程就可以按时执行布局和绘制。

- **第二种优化方式：**
  栅格化的整个流程不占用主线程，只在合成器线程和栅格线程中运行，这就意味着它无需和 js 抢占主线程。如果反复进行重绘和重排可能会导致掉帧，这是因为有可能 js 执行阻塞了主线程，而 css 中有个动画属性 transform，通过该属性实现的动画不会经过布局和绘制，而是直接运行在合成器线程和栅格线程，所以不会受到主线程 js 执行的影响。更重要的是通过 transform 实现的动画由于不需要经过布局绘制样式计算等操作，所以节省了很多运算事件。

#### 13.4 避免重绘重排具体方案

**13.4.1 CSS**

- 使用 transform 替代 top 等位移
- 使用 visibility 替换 display:none
- 避免使用 table 布局
- 尽可能在 dom 树的最末端改变 class
- 避免设置多层内联样式，尽量层级扁平
- 将动画效果应用到 position 属性为 absolute 或 fixed 的元素上
- 避免使用 css 表达式
- 将频繁重绘或者回流的节点设置为图层，比如 video、iframe
- css 硬件加速（GPU 加速），可以是 transform:translateZ(0)、opacity、filters、will-change，will-change 提前告诉浏览器元素会发生什么变化

**13.4.2 JS**

- 避免频繁操作样式，合并操作
- 避免频繁操作 dom，合并操作
- 防抖节流控制频率
- 避免频繁读取会引发回流/重绘的属性
- 对具有复杂动画的元素使用绝对定位

### 14. link 标签不会阻塞 DOM 解析但会阻塞 DOM 渲染，script 标签会阻塞 DOM 的解析和渲染

### 15.justify-content:space-between\space-around 有什么区别

- **space-between**

> space-between 使每个块之间产生相同大小的间隔，但不会在容器和块之间产生

![space-between](space-between.png)

- **space-around**

> space-around 则会在每个块的两边产生一个相同大小的间隔，也就是说最外层块和容器之间的间隔大小刚好是两块之间间隔大小的一半（每个块产生的间隔不重叠，所以间隔变成两倍）。

![space-around](space-around.png)

### 16.flex 布局，一左一右

- justify-content: space-between;

```
{
    display: flex;
    flex-flow:  row nowrap;
    justify-content: space-between;
}
```

- 给需要在右边显示的元素加 margin-left: auto;
