---
title: vueRouter
date: 2020-01-10 15:55:22
categories:
  - vueRouter
tags:
  - vueRouter
  - 面试
---

#### 1 vue-router 是什么?它有哪些组件

**vue-router 是 vue 的路由插件**

**组件**：`<router-link>` `<router-view> `

#### 2 active-class 是哪个组件的属性？

**active-class** 是 vue-router 模块的 router-link 组件的属性

##### active-class 使用方法

- 直接在路由 js 文件中配置 linkActiveClass

```
export default new Router({

  linkExactActiveClass: 'active',

})
```

- 在 router-link 中写入 exact

```
<router-link to="/" class="menu-home" active-class="active" exact>首页</router-link>
```

<!--more-->

#### 3.怎么定义 vue-router 的动态路由? 怎么获取传过来的值

**在 router 目录下的 index.js 文件中，对 path 属性加上 /:id。**

获取传过来的值：**使用 router 对象的 params.id 获取**

示例

- 第一个页面

```
<div>
    <a href="javascript:void(0)" @click="getDetail(1)">
        去第二个页面
    </a>
</div>

<script>
export default {
  name: 'app',
  methods:{
    getDetail(id) {
      this.$router.push({
        name: 'login',
        params: {
          id: id
        }
      })
    }
  }
}
</script>
```

- 第二个页面

```
export default {
  name: 'login',
  data () {
    return {
      uid:this.$route.params.id,
      msg: '这是第二个页面'
    }
  },
 mounted: function() {
            console.log(this.uid);
  },
}
```

- 路由定义（解决如果刷新 login 页面，将失去传值）

```
{
    path: ':id',
    component: login
}
```

#### 4.vue-router 有哪几种导航钩子?

##### 4.1 导航钩子的作用

vue-router 的导航钩子，主要用来作用是拦截导航，让他完成跳转或取消。

##### 4.2 植入路由导航的方式

- 全局的
- 单个路由独享的
- 组件级的

**1.全局导航钩子**

全局导航钩子主要有两种钩子：`前置守卫`、`后置钩子`

注册一个全局前置守卫：

```
const router = new VueRouter({ ... });
router.beforeEach((to, from, next) => {
    // do someting
});
```

这三个参数 to 、from 、next 分别的作用：

**to**: Route，代表要进入的目标，它是一个路由对象

**from**: Route，代表当前正要离开的路由，同样也是一个路由对象

**next**: Function，这是一个必须需要调用的方法，而具体的执行效果则依赖 next 方法调用的参数：

- **next()**：进入管道中的下一个钩子，如果全部的钩子执行完了，则导航的状态就是 confirmed（确认的）
- **next(false)**：这代表中断掉当前的导航，即 to 代表的路由对象不会进入，被中断，此时该表 URL 地址会被重置到 from 路由对应的地址
- **next(‘/’)** 和 **next({path: ‘/’})**：在中断掉当前导航的同时，跳转到一个不同的地址
- **next(error)**：如果传入参数是一个 Error 实例，那么导航被终止的同时会将错误传递给 router.onError() 注册过的回调

**注意**：next 方法必须要调用，否则钩子函数无法 resolved

对于全局后置钩子：

```
router.afterEach((to, from) => {
    // do someting
});
```

不同于前置守卫，后置钩子并没有 next 函数，也不会改变导航本身

**4.3 路由独享的钩子 beforeEnter**

顾名思义，即单个路由独享的导航钩子，它是在路由配置上直接进行定义的：

```
cont router = new VueRouter({
    routes: [
        {
            path: '/file',
            component: File,
            beforeEnter: (to, from ,next) => {
                // do someting
            }
        }
    ]
});
```

至于他的参数的使用，和全局前置守卫是一样的

**4.4 组建内的导航钩子**

组件内的导航钩子主要有这三种：`beforeRouteEnter`、`beforeRouteUpdate`、`beforeRouteLeave`。他们是直接在路由组件内部直接进行定义的

我们看一下他的具体用法：

```
const File = {
    template: `<div>This is file</div>`,
    beforeRouteEnter(to, from, next) {
        // do someting
        // 在渲染该组件的对应路由被 confirm 前调用
    },
    beforeRouteUpdate(to, from, next) {
        // do someting
        // 在当前路由改变，但是依然渲染该组件是调用
    },
    beforeRouteLeave(to, from ,next) {
        // do someting
        // 导航离开该组件的对应路由时被调用
    }
}
```

需要注意是：

beforeRouteEnter 不能获取组件实例 this，因为当守卫执行前，组件实例被没有被创建出来，剩下两个钩子则可以正常获取组件实例 this

但是并不意味着在 beforeRouteEnter 中无法访问组件实例，我们可以通过给 next 传入一个回调来访问组件实例。在导航被确认是，会执行这个回调，这时就可以访问组件实例了，如：

```
beforeRouteEnter(to, from, next) {
    next (vm => {
        // 这里通过 vm 来访问组件实例解决了没有 this 的问题
    })
}
```

注意，仅仅是 beforRouteEnter 支持给 next 传递回调，其他两个并不支持。因为归根结底，支持回调是为了解决 this 问题，而其他两个钩子的 this 可以正确访问到组件实例，所有没有必要使用回调

最后是完整的导航解析流程： 1.导航被触发 2.在失活的组件里调用离开守卫 3.调用全局的 beforeEach 守卫 4.在重用的组件里调用 beforeRouteUpdate 守卫 5.在路由配置里调用 beforEnter 6.解析异步路由组件 7.在被激活的组件里调用 beforeRouteEnter 8.调用全局的 beforeResolve 守卫 9.导航被确认 10.调用全局的 afterEach 钩子 11.触发 DOM 更新 12.在创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数

#### 5.$route 和 $router 的区别

router 为 VueRouter 的实例，相当于一个全局的路由器对象，里面含有很多属性和子对象，例如 history 对象。。。经常用的跳转链接就可以用 this.$router.push，和 router-link 跳转一样。。。

route 相当于当前正在跳转的路由对象。。可以从里面获取 name,path,params,query 等。。

#### 6.vue-router 响应路由参数的变化

提醒一下，当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。

复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch (监测变化) $route 对象：

```
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```

或者：

```
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

注意是：

（1）从同一个组件跳转到同一个组件。

（2）生命周期钩子 created 和 mounted 都不会调用。

#### 7.vue-router 传参

##### 7.1 query 方式传参和接收参数

传参:

```
this.$router.push({
        path:'/xxx',
        query:{
          id:id
        }
      })
```

接收参数:
this.$route.query.id
注意:传参是this.$router,接收参数是 this.$route,这里千万要看清了！！！

##### 7.2 params 方式传参和接收参数

传参:

```
this.$router.push({
        name:'xxx',
        params:{
          id:id
        }
      })
```

接收参数:
this.$route.params.id

注意:params 传参，push 里面只能是 name:‘xxxx’,不能是 path:’/xxx’,因为 params 只能用 name 来引入路由，如果这里写成了 path，接收参数页面会是 undefined！！！

另外，二者还有点区别，直白的来说 query 相当于 get 请求，页面跳转的时候，可以在地址栏看到请求参数，而 params 相当于 post 请求，参数不会再地址栏中显示

#### 8.vue-router 的两种模式（hash，history）

vue-router 有两种模式，hash 模式和 history 模式

**8.1 hash 模式（vue-router 默认 hash 模式）**
hash 模式背后的原理是 onhashchange 事件。

```
window.onhashchange=function(){
 let hash=location.hash.slice(1);
 document.body.style.color=hash;
}
```

（localtion 是 js 里管理地址栏的内置对象，是 window 对象的一部分，可通过 window.localtion 访问，在 w3cshool 里的详细介绍)
由于 hash 发生变化的 url 都会被浏览器记录下来，使得浏览器的前进后退都可以使用了，尽管浏览器没有亲求服务器，但是页面状态和 url 关联起来。后来人们称其为前端路由，成为单页应用标配。
比如http://www.abc.com/#/index，hash值为#/index。hash模式的特点在于hash出现在url中，但是不会被包括在HTTP请求中，对后端没有影响，不会重新加载页面。

**8.2 history 模式**
history 模式利用了 HTML5 History Interface 中新增的 pushState()和 replaceState()方法。MDN 相关介绍（需要特定浏览器支持）。这两个方法应用于浏览器的历史记录栈，提供了对历史记录进行修改的功能。只是当他们进行修改时，虽然修改了 url，但浏览器不会立即向后端发送请求。
当使用 history 模式时，url 就像正常的 url,例如http://abc.com/user/id相比hash模式更加好看。特别注意，history模式需要后台配置支持。如果后台没有正确配置，访问时会返回404。
通过 history api，我们丢弃了丑陋的#，但是有一个缺点，当刷新时，如果服务器中没有相应的相应或者资源，会分分钟刷出一个 404 来（刷新需要请求服务器）。所以 history 模式不怕前进，不怕后退，就怕刷新。

3 hash 模式和 history 模式相比较
\*pushState()设置新的 url 可以是和当前 url 同源的任意 url;hash 只可修改#后面的部分，只能设置当前 url 同文档的 url。

pushState()设置的新 url 可与当前 url 一致，这样也会把记录添加到栈中；hash 必须设置与当前 url 不同的 url 的，才会触发动作将记录添加到栈中。
pushState()通过 stateObject 参数可以添加任意类型的数据到记录中；hash 只可添加短字符串。
pushState()可额外设置 title 属性供后续使用。
不过，hash 模式也有比 history 模式优势的地方。
hash 模式下，仅 hash 符号之前的 url 会被包含在请求中，后端如果没有做到对路由的全覆盖，也不会返回 404 错误。
history 模式下，前端的 url 必须和实际向后端发起请求的 url 一致，如http://abc.com/user/id,后端如果没有对user/id的路由处理，将返回404错误。

#### 9.vue-router 实现路由懒加载（ 动态加载路由 ）

结合 Vue 的异步组件和 Webpack 的代码分割功能，轻松实现路由组件的懒加载

在项目 router/index.js 文件中将

```
import Recommend from 'components/recommend/recommend'
```

更改为

//方法 1：

```
const Recommend = () => import('components/recommend/recommend')
```

//方法 2：

```
const Recommend = (resolve) => {
  import('components/recommend/recommend').then((module) => {
    resolve(module)
  })
}
```

即可实现路由懒加载的效果

#### 10.vue-router 怎么重定向页面？

路由中配置 redirect 属性

#### 11. vue-router 怎么配置 404 页面？

```
// router.js
export default new Router({
  mode: 'history',
  routes: [
    // ...
    {
      name: '404',
      path: '/404',
      component: () => import('@/views/notFound.vue')
    },
    {
      path: '*',    // 此处需特别注意至于最底部
      redirect: '/404'
    }
  ],
})
```

#### 12. vue 跳转新路由 滚动到固定位置

```
export default new Router({
  // mode: 'history',  //可以去掉url中的#。但是打包后需要后台配置，否则会404
  routes: routerMap,
  scrollBehavior(to, from, savedPosition) { //设置回到顶部
    if (savedPosition) {
        return savedPosition
    }
    return {x: 0, y: 0}
  }
})
```

#### 13. vue 路由去掉#

路由代码中添加 mode：‘history’

```vue
const router = new Router({ mode: 'history', //去掉url中的# )}
```
