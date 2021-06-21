---
title: CSS 一
date: 2019-11-02 17:29:22
categories:
  - 面试
tags:
  - Css
  - 面试
---

**CSS 面试总结分为三部分，本节为第一部分：**

### 1.伪类与伪元素的区别

相同点：

- 伪类与伪元素都不出现在源文件和 DOM 树中。也就是说在 html 源文件中是看不到伪类和伪元素的。

不同点：

- css2.0 中伪类、伪元素都是以单冒号:表示。
- css2.1 后规定伪类用单冒号表示，伪元素用双冒号::表示。
- 伪类其实就是基于普通 DOM 元素而产生的不同状态，它是 DOM 元素的某一特征。
- 伪元素能够创建在 DOM 树中不存在的抽象对象，而且这些抽象对象是能够访问到的。

<!--more-->

### 2.css 盒模型

**w3c 的盒模型的构成：content padding border margin**

**IE 盒模型与标准盒模型**

- IE 模型与标准模型唯一的区别是内容计算方式的不同：

- IE 模型：width = content + padding， box-sizing: border-box;

- 标准模型：width = content，box-sizing: content-box;

### 3.css 实现 div 宽度自适应，宽高保持等比缩放

- 优点：简洁方便；缺点：需要注意兼容问题

```css
.square {
  width: 30%;
  height: 30vh;
  background: red;
}
```

- 使用 padding 来实现

```css
.square {
  width: 30%;
  height: 0;
  padding-bottom: 30%;
  background: red;
  /* 这样max-height就失效了 */
  /* max-height: 100px */
}
```

- 利用伪元素的 margin(padding)-top 撑开容器

> 使用垂直方向上的 margin 撑开父元素，仅仅设置伪元素是不够的，这涉及到 margin collapse 外边距合并的概念，由于容器与伪元素在垂直方向上发生了外边距合并，所以撑开父元素高度并没有出现，解决方法是在父元素上触发 BFC：设置 overflow:hidden。

```css
.square {
  width: 30%;
  overflow: hidden;
  background: red;
  /* 这样max-height就生效了 */
  /* max-height: 100px */
}
.square:after {
  content: '';
  display: block;
  margin-top: 100%;
}
```

> 若使用垂直方向上的 padding 撑开父元素，则不需要触发 BFC。子元素的 100%就相当于父元素的 30%。

```css
.square {
  width: 30%;
  overflow: hidden;
  background: red;
  /* 这样max-height就生效了 */
  /* max-height: 100px */
}
.square:after {
  content: '';
  display: block;
  padding-top: 100%;
}
```

### 4.flex:1 的完整写法是？分别是什么意思？

flex 的属性是 flex-grow、flex-shrink、flex-basis 的简写，默认值是 0、1、auto。

- flex-grow 定义项目的放大比例，默认为 0，即如果存在剩余空间，也不放大。
- flex-shrink 定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小。
- flex-basis 给上面两个属性分配多余空间之前，计算项目是否有多余空间，默认值为 auto，即项目本身的大小。

### 5.link 和@import 区别

- link 引用 css 时，在页面载入时同时加载；@import 需要页面网页完全载入以后加载。所以会出现一开始没有 css 样式，闪烁一下出现样式后的页面（网速慢的情况下）。
- link 是 XHTML 标签，除了加载 css 外，还可以定义 RSS 等其他事务；@import 属于 css 范畴，只能加载 css。
- link 是 XHTML 标签，无兼容问题；@import 是在 css2.1 提出的，低版本的浏览器不支持。
- link 支持使用 javascript 控制 DOM 去改变样式，而@import 不支持。

### 6.rem-相对长度单位

浏览器的默认字体都是 16px，那么 1rem = 16px，以此类推计算 12px = 0.75rem，小数点不方便，为了简化 font-size 的换算，我们在根元素 html 中加入 font-size:62.5%;

```css
html {
  font-size: 62.5%;
} /*  公式16px*62.5%=10px  */
```

这样页面中 1rem = 10px，1.2rem = 12px。

### 7.'nth-child'和'nth-type-of'有什么区别

```html
<section>
  <h1>标题</h1>
  <h2>副标题</h2>
  <p>第一个标签</p>
  <p>第二个标签</p>
</section>
<style>
  p:nth-child(2) {
    color: red;
  }
  p:nth-of-type(2) {
    color: green;
  }
</style>
```

- 结果：只有第二个标签字体颜色变绿
  这是因为：p:nth-child(2)不会匹配任何元素，因为此时 section 的第二个元素并不是 p 标签。但是 p:nth-type-of(2)仍然可以正常工作，因为它始终选择的是 section 中的第二个 p 元素。

- 总结：选择器：nth-child(n)先根据后面的数字选中元素的第 n 个子元素，再判断这个子元素是否是前面的选择器，如果是则样式生效，否则无效；而选择器：nth-type-of(n)先在父元素中找出所有符合前面选择器的子元素，再从这些子元素中选择第 n 个子元素（在父元素中有可能不是第 n 个)，如果父元素所有子元素类型都是相同的，那么这两个选择器是没区别的。

### 8.css 方式实现一个不知道宽高的 div 居中都有哪一种方法

- flex 布局

```css
<style>
    .box {
        width: 200px;
        height: 200px;
        border: 1px solid blue;
        position: relative;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .item {
        background: red;
    }
</style>
<div class="box">
    <div class="item">
        <h1 >宽高不定</h1>
    </div>
</div>
```

- position+transform

```css
<style>
    .box {
        width: 200px;
        height: 200px;
        border: 1px solid blue;
        position: relative;
    }

    .item {
        background: red;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }
</style>
<div class="box">
    <div class="item">
        <h1>宽高不定</h1>
    </div>
</div>
```

- position+margin

```css
<style>
.box {
    width: 200px;
    height: 200px;
    border: 1px solid blue;
    position: relative;
}

.item {
    width: 50%;
    height: 50%;
    background: red;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
</style>
<div class="box">
    <div class="item">
        <h1>宽高不定</h1>
    </div>
</div>
```
