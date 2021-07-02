---
title: 前端性能优化
date: 2019-11-14 15:55:22
categories:
  - 前端性能优化
tags:
  - 前端性能优化
  - 面试
---

#### 1 优化 DOM

- 删除不必要的代码和注释包括空格，尽量做到最小化文件。
- 可以利用 GZIP 压缩文件。
- 结合 HTTP 缓存文件。

#### 2 优化 JavaScript

当浏览器遇到 script 标记时，会阻止解析器继续操作，直到 CSSOM 构建完毕，JavaScript 才会运行并继续完成 DOM 构建过程。

- async: 当我们在 script 标记添加 async 属性以后，浏览器遇到这个 script 标记时会继续解析 DOM，同时脚本也不会被 CSSOM 阻止，即不会阻止 CRP。
- defer: 与 async 的区别在于，脚本需要等到文档解析后（ DOMContentLoaded 事件前）执行，而 async 允许脚本在文档解析时位于后台运行（两者下载的过程不会阻塞 DOM，但执行会）。
- 当我们的脚本不会修改 DOM 或 CSSOM 时，推荐使用 async 。
- 预加载 —— preload & prefetch 。
- DNS 预解析 —— dns-prefetch 。

<!--more-->

#### 3 浏览器重绘（Repaint）和回流（Reflow）

回流必将引起重绘，重绘不一定会引起回流。

**重绘（Repaint）**
当页面中元素样式的改变并不影响它在文档流中的位置时（例如：color、background-color、visibility 等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘。

**回流（Reflow）**
当 Render Tree 中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程称为回流。
会导致回流的操作：

- 页面首次渲染
- 浏览器窗口大小发生改变
- 元素尺寸或位置发生改变元素内容变化（文字数量或图片大小等等）
- 元素字体大小变化
- 添加或者删除可见的 DOM 元素
- 激活 CSS 伪类（例如:hover）
- 查询某些属性或调用某些方法
- 一些常用且会导致回流的属性和方法
  clientWidth、clientHeight、clientTop、clientLeftoffsetWidth、offsetHeight、offsetTop、offsetLeftscrollWidth、scrollHeight、scrollTop、scrollLeftscrollIntoView()、scrollIntoViewIfNeeded()、getComputedStyle()、
  getBoundingClientRect()、scrollTo()

**如何避免 CSS**

避免使用 table 布局。
尽可能在 DOM 树的最末端改变 class。
避免设置多层内联样式。
将动画效果应用到 position 属性为 absolute 或 fixed 的元素上。
避免使用 CSS 表达式（例如：calc()）。
Javascript

避免频繁操作样式，最好一次性重写 style 属性，或者将样式列表定义为 class 并一次性更改 class 属性。

#### 4 图片懒加载

图片懒加载在一些图片密集型的网站中运用比较多，通过图片懒加载可以让一些不可视的图片不去加载，避免一次性加载过多的图片导致请求阻塞（浏览器一般对同一域名下的并发请求的连接数有限制），这样就可以提高网站的加载速度，提高用户体验。

原理
将页面中的 img 标签 src 指向一张小图片或者 src 为空，然后定义 data-src（这个属性可以自定义命名，我才用 data-src）属性指向真实的图片。src 指向一张默认的图片，否则当 src 为空时也会向服务器发送一次请求。可以指向 loading 的地址。注意，图片要指定宽高。

```
<img src="default.jpg" data-src="666.jpg" />
```

当载入页面时，先把可视区域内的 img 标签的 data-src 属性值赋给 src，然后监听滚动事件，把用户即将看到的图片加载。这样便实现了懒加载。

#### 5 渲染完成后的页面交互优化：

防抖（debounce）/节流（throttle）
防抖（debounce）
输入搜索时，可以用防抖 debounce 等优化方式，减少 http 请求；

#### 6 SPA 首屏优化方式

- 减小入口文件体积
- 静态资源本地缓存
- UI 框架按需加载
- 图片资源的压缩
- 组件重复打包
- 开启 GZip 压缩
- 使用 SSR

- 减小入口文件体积

常用的手段是路由懒加载，把不同路由对应的组件分割成不同的代码块，待路由被请求的时候会单独打包路由，使得入口文件变小，加载速度大大增加

在 vue-router 配置路由的时候，采用动态加载路由的形式

```
routes:[
    path: 'Blogs',
    name: 'ShowBlogs',
    component: () => import('./components/ShowBlogs.vue')
]
```

以函数的形式加载路由，这样就可以把各自的路由文件分别打包，只有在解析给定的路由时，才会加载路由组件

- 静态资源本地缓存
  后端返回资源问题：

采用 HTTP 缓存，设置 Cache-Control，Last-Modified，Etag 等响应头

采用 Service Worker 离线缓存

前端合理利用 localStorage

- UI 框架按需加载
  在日常使用 UI 框架，例如 element-UI、或者 antd，我们经常性直接引用整个 UI 库

```
import ElementUI from 'element-ui'
Vue.use(ElementUI)
但实际上我用到的组件只有按钮，分页，表格，输入与警告 所以我们要按需引用

import { Button, Input, Pagination, Table, TableColumn, MessageBox } from 'element-ui';
Vue.use(Button)
Vue.use(Input)
Vue.use(Pagination)
```

- 组件重复打包
  假设 A.js 文件是一个常用的库，现在有多个路由使用了 A.js 文件，这就造成了重复下载

解决方案：在 webpack 的 config 文件中，修改 CommonsChunkPlugin 的配置

```
minChunks: 3
```

minChunks 为 3 表示会把使用 3 次及以上的包抽离出来，放进公共依赖文件，避免了重复加载组件

- 图片资源的压缩
  图片资源虽然不在编码过程中，但它却是对页面性能影响最大的因素

对于所有的图片资源，我们可以进行适当的压缩

对页面上使用到的 icon，可以使用在线字体图标，或者雪碧图，将众多小图标合并到同一张图上，用以减轻 http 请求压力。

- 开启 GZip 压缩
  拆完包之后，我们再用 gzip 做一下压缩 安装 compression-webpack-plugin

cnmp i compression-webpack-plugin -D
在 vue.congig.js 中引入并修改 webpack 配置

```
const CompressionPlugin = require('compression-webpack-plugin')

configureWebpack: (config) => {
        if (process.env.NODE_ENV === 'production') {
            // 为生产环境修改配置...
            config.mode = 'production'
            return {
                plugins: [new CompressionPlugin({
                    test: /\.js$|\.html$|\.css/, //匹配文件名
                    threshold: 10240, //对超过10k的数据进行压缩
                    deleteOriginalAssets: false //是否删除原文件
                })]
            }
        }
在服务器我们也要做相应的配置 如果发送请求的浏览器支持gzip，就发送给它gzip格式的文件 我的服务器是用express框架搭建的 只要安装一下compression就能使用

const compression = require('compression')
app.use(compression())  // 在其他中间件使用之前调用
```

- 使用 SSR
  SSR（Server side ），也就是服务端渲染，组件或页面通过服务器生成 html 字符串，再发送到浏览器

从头搭建一个服务端渲染是很复杂的，vue 应用建议使用 Nuxt.js 实现服务端渲染
