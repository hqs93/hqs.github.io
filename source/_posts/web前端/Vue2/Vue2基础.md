---
title: Vue2基础
categories: 
- [web前端, Vue2, Vue2基础]
---

# Vue2基础

<!-- more -->

### Vue异步组件

***

### Vue组件通信

***

### Vue导航守卫

#### 全局守卫
 - to: 即将要进入的目标路由对象
 - from: 当前导航正要离开的路由对象
 - next: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数
```javascript
next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed （确认的）。

next(false): 中断当前的导航。如果浏览器的 URL 改变了（可能是用户手动或者浏览器后退按钮），那么 URL 地址会重置到 from 路由对应的地址。

next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。

next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。
```

 - 前置守卫
```javascript
const router = new VueRouter({ ... })
router.beforeEach((to, from, next) => {})
```

 - 后置守卫
```javascript
router.afterEach((to, from) => {})
```

 - 解析守卫
在 2.5.0+ 你可以用 router.beforeResolve 注册一个全局守卫。这和 router.beforeEach 类似，区别是在导航被确认之前，同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用。
```javascript
router.beforeResolve((to, from, next) => {})
```

#### 路由独享的守卫
 - 在路由配置上直接定义 beforeEnter 守卫
```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

#### 组件内的守卫
```javascript
export default {  
  name: ''
  data: {
      return {
          //...
      }
  },
  methods: {
      // ...
  }
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

***

### Vue组件的生命周期(11个钩子函数)

- **1.beforeCreate** 在实例初始化之后,数据观测 (data observer) 之前被调用.
- **2.created** 实例已经创建完成之后被调用,在这一步,实例已完成以下的配置: 数据观测 (data observer) ,属性和方法的运算, watch/event 事件回调,这里没有 $el.
- **3.beforeMount** 在挂载开始之前被调用: 相关的 render 函数首次被调用,可以理解为 template.
- **4.mounted** el 被新创建的 vm.$el 替换,并挂载到实例上去之后调用该钩子.
- **5.beforeUpdate** 数据更新时调用,发生在虚拟 DOM 重新渲染和打补丁之前.
- **6.updated** 由于数据更改导致的虚拟 DOM 重新渲染和打补丁,在这之后会调用该钩子.
- **7.beforeDestroy** 实例销毁之前调用,在这一步实例仍然完全可用.
- **8.destroyted** Vue实例销毁后调用,调用后 Vue 实例指示的所有东西都会解绑定,所有的事件监听器会被移除,所有的子实例也会被销毁,该钩子在服务器端渲染期间不被调用.
- **9.errorCaptured** 当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 false 以阻止该错误继续向上传播。
- **10.activated** 激活状态,当前组件显示时执行的生命周期
- **11.deactivated** 缓存状态,当前组件缓存时执行的生命周期
- **keep-alive** Vue的内置组件，其中的生命周期只会走一遍，并且会增加activated和deactivaed生命周期，用以其他的业务逻辑。通常用来包裹动态切换的路由或组件，防止组件频繁地创建和销毁，从而达到性能优化的效果。

#### Vue性能优化

##### 一.源码优化

1.**代码模块化**,把重复的代码单独封装,提高代码复用性

2.**for循环设置key值**，在用v-for进行数据遍历渲染的时候，为每一项都设置唯一的key值，为了让Vue内部核心代码能更快地找到该条数据，当旧值和新值去对比的时候，可以更快的定位到diff。

3.**Vue异步组件**，路由设置成懒加载，当首屏渲染的时候，能够加快渲染速度，和ajax的原理类似，通过异步加载来避免运行时的阻塞。

4.**组件销毁**,不要造成内部泄漏，使用过后的全局变量在组件销毁后重新置为null。

5.**使用keep-alive缓存**,keep-alive是Vue提供的一个比较抽象的组件，用来对组件进行缓存，从而节省性能。
keep-alive常用的属性: include：字符串或正则表达式。匹配的组件会被缓存。
                    exclude：字符串或正则表达式。匹配的组件都不会被缓存。
                    max：数字。最多可以缓存多少组件实例。

##### 二.打包优化

1.**修改vue.config.js中的配置项**,把productionSourceMap设置为false，不然最终打包过后会生成一些map文件，如果不关掉，生成环境是可以通过map去查看源码的，并且可以开启gzip压缩，使打包过后体积变小。

2.**使用cdn的方式外部加载一些资源**,比如vue-router、axios等Vue的周边插件，在webpack.config.js里面，externals里面设置一些不必要打包的外部引用模块。然后在入门文件index.html里面通过cdn的方式去引入需要的插件。

3.**减少图片使用**，因为对于网页来说，图片会占用很大一部分体积，所以，优化图片的操作可以有效的来加快加载速度。可以用一些css3的效果来代替图片效果，或者使用雪碧图来减少图片的体积。

4.**按需引入**，咱们使用的一些第三方库可以通过按需引入的方式加载。避免引入不需要使用的部分，无端增加项目体积。比如在使用element-ui库的时候，可以只引入需要用到的组件。

#### 每个生命周期内部可以做什么事.

- created 实例已经创建完成,因为它是最早触发的原因可以进行一些数据,资源的请求(ajax).
- mounted 实例已经挂载完成,可以进行一些 DOM 操作.
- beforeUpdate 可以在这个钩子中进一步的更改状态,这不会触发附加的重渲染过程.
- updated 可以执行依赖于 DOM 的操作,然而在大多数情况下,应该避免在此期间更改状态,因为这可能会导致更新无限循环,该钩子在服务器端渲染期间不被调用.
- destroyed 可以执行一些优化操作,清空定时器,接触绑定事件.

#### 请求适合放在哪个生命周期中

- 在 created 的时候,视图中的 dom 并没有渲染出来,所以此时如果直接去操作 dom 节点,无法找到相关的元素.
- 在 mounted 中,由于此时 dom 已经渲染出来了,所以可以直接操作 dom 节点
- 一般情况下都放到 mounted 中,保证逻辑的统一性,因为生命周期是同步执行的, ajax 是异步执行的. 

服务端渲染不支持 mounted 方法,所以在服务端渲染的情况下统一放到 created 中.**

***

### v-for为什么要加key

vue和react的虚拟DOM的Diff算法大致相同，其核心是基于两个简单的假设
首先讲一下diff算法的处理方法，对操作前后的dom树同一层的节点进行对比，一层一层对比,如果一层中有多个节点,继续层层对比,当我们需要修改其中一个节点时,如果使用这种对比方式无疑是非常消耗性能的,所以必须绑定索引值来标识组件的唯一性,方便更好更快的区别各个组件来更新虚拟DOM.

![img](https://upload-images.jianshu.io/upload_images/3973616-25f6c171772b50b6.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/452/format/webp)

***

### vue中$el等属性：

##### vm.$el

获取Vue实例关联的DOM元素；

##### vm.$data

获取Vue实例的data选项（对象）

##### vm.$options

获取Vue实例的自定义属性（如vm.$options.methods,获取Vue实例的自定义属性methods）

##### vm.$refs

获取页面中所有含有ref属性的DOM元素（如vm.$refs.hello，获取页面中含有属性ref = “hello”的DOM元素，如果有多个元素，那么只返回最后一个）

```JavaScript
var app   = new Vue({    
        el:"#container",    
        data:{    
                msg:"hello,2018!"    
        },    
        address:"长安西路"    
})    
```

##### console.log(app.$el);

返回Vue实例的关联DOM元素，在这里是#container

##### console.log(app.$data);

返回Vue实例的数据对象data，在这里就是对象{msg：”hello，2018“}

##### console.log(app.$options.address);

返回Vue实例的自定义属性address，在这里是自定义属性address

##### console.log(app.$refs.hello)

返回含有属性ref = hello的DOM元素（如果多个元素都含有这样的属性，只返回最后一个）<h3 ref = "hello">呵呵 1{{msg}}</h3>

***

### Vue的响应式数据原理:Object.defineProperty

- [js设计模式之【观察者模式】&【发布/订阅模式模式】](https://www.cnblogs.com/leaf930814/p/9014200.html)

Vue会遍历实例的data属性，把每一个data都设置为访问器，然后在该属性的getter函数中将其设为watcher，在setter中向其他watcher发布改变的消息。

```JavaScript
//遍历传入实例的data对象的属性，将其设置为Vue对象的访问器属性
 function observe(obj,vm){
     Object.keys(obj).forEach(function(key){
         defineReactive(vm,key,obj[key]);
     });
 }
 //设置为访问器属性，并在其getter和setter函数中，使用订阅发布模式。互相监听。
 function defineReactive(obj,key,val){
     //这里用到了观察者模式,它定义了一种一对多的关系，让多个观察者监听一个主题对象，这个主题对象的状态发生改变时会通知所有观察者对象，观察者对象就可以更新自己的状态。
     //实例化一个主题对象，对象中有空的观察者列表
     var dep = new Dep();
     //将data的每一个属性都设置为Vue对象的访问器属性，属性名和data中相同
     //所以每次修改Vue.data的时候，都会调用下边的get和set方法。然后会监听v-model的input事件，当改变了input的值，就相应的改变Vue.data的数据，然后触发这里的set方法
     Object.defineProperty(obj,key,{
         get: function(){
             //Dep.target指针指向watcher，增加订阅者watcher到主体对象Dep
             if(Dep.target){
                 dep.addSub(Dep.target);
             }
             return val;
         },
         set: function(newVal){
             if(newVal === val){
                 return
             }
             val = newVal;
             //console.log(val);
             //给订阅者列表中的watchers发出通知
             dep.notify();
         }
     });
 }
 
 //主题对象Dep构造函数
 function Dep(){
     this.subs = [];
 }
 //Dep有两个方法，增加订阅者  和  发布消息
 Dep.prototype = {
     addSub: function(sub){
         this.subs.push(sub);
     },
     notify: function(){
         this.subs.forEach(function(sub){
             sub.update();
         });
     }
 }
```

***

### Vue.js原理分析之observer模块详解

observer模块在Vue项目中的代码位置是src/core/observer，模块共分为这几个部分：

1. Observer: 数据的观察者，让数据对象的读写操作都处于自己的监管之下
2. Watcher: 数据的订阅者，数据的变化会通知到Watcher，然后由Watcher进行相应的操作，例如更新视图
3. Dep: Observer与Watcher的纽带，当数据变化时，会被Observer观察到，然后由Dep通知到Watcher
   

**示意图如下：**

![img](https://files.jb51.net/file_images/article/201702/201721790331261.png?20171179344)

**Observer**

Observer类定义在src/core/observer/index.js中，先来看一下Observer的构造函数

```JavaScript
constructor (value: any) {
 this.value = value
 this.dep = new Dep()
 this.vmCount = 0
 def(value, '__ob__', this)
 if (Array.isArray(value)) {
 const augment = hasProto
 ? protoAugment
 : copyAugment
 augment(value, arrayMethods, arrayKeys)
 this.observeArray(value)
 } else {
 this.walk(value)
 }
}
```

value是需要被观察的数据对象，在构造函数中，会给value增加__ob__属性，作为数据已经被Observer观察的标志。如果value是数组，就使用observeArray遍历value，对value中每一个元素调用observe分别进行观察。如果value是对象，则使用walk遍历value上每个key，对每个key调用defineReactive来获得该key的set/get控制权。

**解释下上面用到的几个函数的功能：**

- observeArray: 遍历数组，对数组的每个元素调用observe
- observe: 检查对象上是否有__ob__属性，如果存在，则表明该对象已经处于Observer的观察中，如果不存在，则new Observer来观察对象（其实还有一些判断逻辑，为了便于理解就不赘述了）
- walk: 遍历对象的每个key，对对象上每个key的数据调用defineReactive
- defineReactive:  通过Object.defineProperty设置对象的key属性，使得能够捕获到该属性值的set/get动作。一般是由Watcher的实例对象进行get操作，此时Watcher的实例对象将被自动添加到Dep实例的依赖数组中，在外部操作触发了set时，将通过Dep实例的notify来通知所有依赖的watcher进行更新。

如果不太理解上面的文字描述可以看一下图：

[![img](https://files.jb51.net/file_images/article/201702/201721791110599.png?201711791120)](https://files.jb51.net/file_images/article/201702/201721791110599.png?201711791120)

**Dep**

Dep是Observer与Watcher之间的纽带，也可以认为Dep是服务于Observer的订阅系统。Watcher订阅某个Observer的Dep，当Observer观察的数据发生变化时，通过Dep通知各个已经订阅的Watcher。

**Dep提供了几个接口：**

- addSub: 接收的参数为Watcher实例，并把Watcher实例存入记录依赖的数组中
- removeSub: 与addSub对应，作用是将Watcher实例从记录依赖的数组中移除
- depend: Dep.target上存放这当前需要操作的Watcher实例，调用depend会调用该Watcher实例的addDep方法，addDep的功能可以看下面对Watcher的介绍
- notify: 通知依赖数组中所有的watcher进行更新操作

**Watcher**

Watcher是用来订阅数据的变化的并执行相应操作（例如更新视图）的。Watcher的构造器函数定义如下：

```JavaScript
constructor (vm, expOrFn, cb, options) {
 this.vm = vm
 vm._watchers.push(this)
 // options
 if (options) {
 this.deep = !!options.deep
 this.user = !!options.user
 this.lazy = !!options.lazy
 this.sync = !!options.sync
 } else {
 this.deep = this.user = this.lazy = this.sync = false
 }
 this.cb = cb
 this.id = ++uid // uid for batching
 this.active = true
 this.dirty = this.lazy // for lazy watchers
 this.deps = []
 this.newDeps = []
 this.depIds = new Set()
 this.newDepIds = new Set()
 this.expression = process.env.NODE_ENV !== 'production'
 ? expOrFn.toString()
 : ''
 if (typeof expOrFn === 'function') {
 this.getter = expOrFn
 } else {
 this.getter = parsePath(expOrFn)
 if (!this.getter) {
 this.getter = function () {}
 process.env.NODE_ENV !== 'production' && warn(
 `Failed watching path: "${expOrFn}" ` +
 'Watcher only accepts simple dot-delimited paths. ' +
 'For full control, use a function instead.',
 vm
 )
 }
 }
 this.value = this.lazy
 ? undefined
 : this.get()
}
```

参数中，vm表示组件实例，expOrFn表示要订阅的数据字段（字符串表示，例如a.b.c）或是一个要执行的函数，cb表示watcher运行后的回调函数，options是选项对象，包含deep、user、lazy等配置。

**watcher实例上有这些方法：**

- get: 将Dep.target设置为当前watcher实例，在内部调用this.getter，如果此时某个被Observer观察的数据对象被取值了，那么当前watcher实例将会自动订阅数据对象的Dep实例
- addDep: 接收参数dep(Dep实例)，让当前watcher订阅dep
- cleanupDeps: 清除newDepIds和newDep上记录的对dep的订阅信息
- update: 立刻运行watcher或者将watcher加入队列中等待统一flush
- run: 运行watcher，调用this.get()求值，然后触发回调
- evaluate: 调用this.get()求值
- depend: 遍历this.deps，让当前watcher实例订阅所有dep
- teardown: 去除当前watcher实例所有的订阅
  

**Array methods**

在src/core/observer/array.js中，Vue框架对数组的push、pop、shift、unshift、sort、splice、reverse方法进行了改造，在调用数组的这些方法时，自动触发dep.notify()，解决了调用这些函数改变数组后无法触发更新的问题。

在Vue的官方文档中对这个也有说明：http://cn.vuejs.org/v2/guide/list.html#变异方法

***

### Vue.set 解决视图更新(v-for动态更新数据)

当vue的data里边声明或者已经赋值过的对象或者数组（数组里边的值是对象）时，向对象中添加新的属性，如果更新此属性的值，是不会更新视图的。

```JavaScript
<template>
 <div id="app">
  <p v-for="item in items" :key="item.id">{{item.message}}</p>
  <button class="btn" @click="handClick()">更改数据</button>
 </div>
</template>
 
<script>
export default {
 name: 'App',
 data () {
  return {
   items: [
        { message: "one", id: "1" },
        { message: "two", id: "2" },
        { message: "three", id: "3" }
      ]
  }
 },
 mounted () {
   this.items[0] = { message:'first',id:'4'} //此时对象的值更改了，但是视图没有更新
  // let art = {message:'first',id:"4"}
  // this.$set(this.items,0,art) //$set 可以触发更新视图
 },
 methods: {
  handClick(){
   let change = this.items[0]
   change.message="shen"
   this.$set(this.items,0,change)
  }
 }
}
</script>
 
<style>
 
</style>
```

**调用方法: Vue.set( target , key , value)
**

- target: 要更改的数据源（可以是一个对象或者数组）
- key 要更改的具体数据 （索引）
- value 重新赋的值

***

### vue前端权限控制

#### 1.权限的分类

 - 后端权限: 从根本上讲前端仅仅只是视图层的展示,权限的核心是在于服务器中的数据变化,所以后端才是权限的关键,后端权限可以控制某个用户是否能够查询数据,是否能够修改数据等操作.
后端严重权限的方式
```javascript
cookie
session
token
```
后端的权限设计RBAC
```javascript
用户
角色
权限
```

 - 前端权限
前端权限的控制本质上来说,就是控制前端的视图层的展示和前端所发送的请求,但是只有前端权限控制没有后端权限控制是万万不可的,前端权限控制只可以说是达到锦上添花的效果.

#### 2.前端权限的意义
 - 降低非法操作的可能性
 - 尽可能排除不必要请求,减轻服务器压力
 - 提高用户体验

#### 3.前端权限控制思路
 - 菜单的控制
在登陆请求中,会得到权限数据,前端根据权限数据,展示对应的菜单.
权限的数据需要在多组件之间共享,因此采用vuex
防止刷新界面,权限数据丢失,所以存储在sessionStorage,并且保证两者的同步

 - 界面的控制
如果用户未登录,并手动输入界面地址,则跳转到登陆界面,如果用户已登陆,并手动输入非权限内的地址,则跳转到404界面
路由的导航守卫可以防止跳过登陆界面
动态路由可以让不具备权限的界面的路由规则不存在

 - 按钮(交互功能)的控制
根据权限数据,展示可进行交互的功能
路由规则中可以增加路由元数据,用来控制
自定义指令可以很方便的实现按钮控制

 - 请求和响应的控制
如果用户通过非常规操作,比如通过浏览器调试工具改变按钮的启用状态,此时发送的请求应该被前端拦截.
请求拦截器和响应拦截器的使用
请求方式约定 restful规范

***

### Vue 相关库

1. element - 饿了么出品的Vue2的web UI工具套件
2. mint-ui - Vue 2的移动UI元素
3. iview - 基于 Vuejs 的开源 UI 组件库
4. Keen-UI - 轻量级的基本UI组件合集
5. vue-material - 通过Vue Material和Vue 2建立精美的app应用
6. muse-ui - 三端样式一致的响应式 UI 库
7. vuetify - 为移动而生的Vue JS 2组件框架
8. vonic - 快速构建移动端单页应用
9. vue-blu - 帮助你轻松创建web应用
10. vue-multiselect - Vue.js选择框解决方案
11. VueCircleMenu - 漂亮的vue圆环菜单
12. vue-chat - vuejs和vuex及webpack的聊天示例
13. radon-ui - 快速开发产品的Vue组件库
14. vue-waterfall - Vue.js的瀑布布局组件
15. vue-carbon - 基于 vue 开发MD风格的移动端
16. vue-beauty - 由vue和ant design创建的优美UI组件
17. bootstrap-vue - 应用于Vuejs2的Twitter的Bootstrap 4组件
18. vueAdmin - 基于vuejs2和element的简单的管理员模板
19. vue-ztree - 用 vue 写的树层级组件
20. vue-tree - vue树视图组件
21. vue-tabs - 多tab页轻型框架

> **编辑器**

1. markcook - 好看的markdown编辑器
2. eme - 优雅的Markdown编辑器
3. vue-syntax-highlight - Sublime Text语法高亮
4. vue-quill-editor - 基于Quill适用于Vue2的富文本编辑器
5. Vueditor - 所见即所得的编辑器
6. vue-html5-editor - html5所见即所得编辑器
7. vue2-editor - HTML编辑器
8. vue-simplemde - VueJS的Markdown编辑器组件
9. vue-quill - vue组件构建quill编辑器

> **slider**

1. vue-awesome-swiper - vue.js触摸滑动组件
2. vue-slick - 实现流畅轮播框的vue组件
3. vue-swipe - VueJS触摸滑块
4. vue-swiper - 易于使用的滑块组件
5. vue-images - 显示一组图片的lightbox组件
6. vue-carousel-3d - VueJS的3D轮播组件
7. vue-slide - vue轻量级滑动组件
8. vue-slider - vue 滑动组件
9. vue-m-carousel - vue 移动端轮播组件
10. dd-vue-component - 订单来了的公共组件库
11. vue-easy-slider - Vue 2.x的滑块组件

> **图表**

1. vue-table - 简化数据表格
2. vue-chartjs - vue中的Chartjs的封装
3. vue-charts - 轻松渲染一个图表
4. vue-chart - 强大的高速的vue图表解析
5. vue-highcharts - HighCharts组件
6. chartjs - Vue Bulma的chartjs组件
7. vue-chartkick - VueJS一行代码实现优美图表

> **日历**

1. vue-calendar - 日期选择插件
2. vue-datepicker - 日历和日期选择组件
3. vue-datetime-picker - 日期时间选择控件
4. vue2-calendar - 支持lunar和日期事件的日期选择器
5. vue-fullcalendar - 基于vue.js的全日历组件
6. vue-datepicker - 漂亮的Vue日期选择器组件
7. datepicker - 基于flatpickr的时间选择组件
8. vue2-timepicker - 下拉时间选择器
9. vue-date-picker - VueJS日期选择器组件
10. vue-datepicker-simple - 基于vue的日期选择器

> **地址选择**

1. vue-city - 城市选择器
2. vue-region-picker - 选择中国的省份市和地区

> **地图**

1. vue-amap - 基于Vue 2和高德地图的地图组件
2. vue-google-maps - 带有双向数据绑定Google地图组件
3. vue-baidu-map- 基于 Vue 2的百度地图组件库
4. vue-cmap - Vue China map可视化组件

> **播放器**

1. vue-video-player - VueJS视频及直播播放器
2. vue-video - Vue.js的HTML5视频播放器
3. vue-music-master - vue手机端网页音乐播放器

> **滚动scroll**

1. vue-scroller - Vonic UI的功能性组件
2. vue-mugen-scroll - 无限滚动组件
3. vue-infinite-loading - VueJS的无限滚动插件
4. vue-virtual-scroller - 带任意数目数据的顺畅的滚动
5. vue-infinite-scroll - VueJS的无限滚动指令
6. vue-scrollbar - 最简单的滚动区域组件
7. vue-scroll - vue滚动
8. vue-pull-to-refresh - Vue2的上拉下拉
9. mint-loadmore - VueJS的双向下拉刷新组件
10. vue-smoothscroll - smoothscroll的VueJS版本

> **文件上传**

1. vue-upload-component - Vuejs文件上传组件
2. vue-core-image-upload - 轻量级的vue上传插件
3. vue-dropzone - 用于文件上传的Vue组件

> **图片处理**

1. vue-lazyload-img - 移动优化的vue图片懒加载插件
2. vue-image-crop-upload - vue图片剪裁上传组件
3. vue-svgicon - 创建svg图标组件的工具
4. vue-img-loader - 图片加载UI组件
5. vue-image-clip- 基于vue的图像剪辑组件
6. vue-progressive-image - Vue的渐进图像加载插件

> **提示**

1. vue-toast-mobile - VueJS的toast插件
2. vue-msgbox - vuejs的消息框
3. vue-tooltip - 带绑定信息提示的提示工具
4. vue-verify-pop - 带气泡提示的vue校验插件

> **进度条**

1. vue-radial-progress - Vue.js放射性进度条组件
2. vue-progressbar - vue轻量级进度条
3. vue2-loading-bar - 最简单的仿Youtube加载条视图

> **其他**

1. vue-dragging- 使元素可以拖拽
2. Vue.Draggable- 实现拖放和视图模型数组同步
3. vue-picture-input- 移动友好的图片文件输入组件
4. rubik- 基于Vuejs2的开源 UI 组件库
5. VueStar- 带星星动画的vue点赞按钮
6. vue-tables-2- 显示数据的bootstrap样式网格
7. DataVisualization- 数据可视化
8. vue-drag-and-drop-list- 创建排序列表的Vue指令
9. vuwe- 基于微信WeUI所开发的专用于Vue2的组件库
10. vue-typer- 模拟用户输入选择和删除文本的Vue组件
11. vue-impression- 移动Vuejs2 UI元素
12. vue-datatable- 使用Vuejs创建的DataTableView
13. vue-instant- 轻松创建自动提示的自定义搜索控件
14. vue-slider-component- 在vue1和vue2中使用滑块
15. vue-touch-ripple- vuejs的触摸ripple组件
16. coffeebreak- 实时编辑CSS组件工具
17. vue-datasource- 创建VueJS动态表格
18. handsontable- 网页表格组件
19. vue-bootstrap-table- 可排序可检索的表格
20. vue-google-signin-button- 导入谷歌登录按钮
21. vue-float-label- VueJS浮动标签模式
22. vue-tagsinput- 基于VueJS的标签组件
23. vue-social-sharing- 社交分享组件
24. vue-popup-mixin- 用于管理弹出框的遮盖层
25. cubeex- 包含一套完整的移动UI
26. vue-fullcalendar- vue FullCalendar封装
27. vue-material-design- Vue MD风格组件
28. vue-morris- Vuejs组件封装Morrisjs库
29. we-vue- Vue2及weui1开发的组件
30. vue-form-2- 全面的HTML表单管理的解决方案
31. vue-side-nav- 响应式的侧边导航
32. mint-indicator- VueJS移动加载指示器插件
33. vue-ripple- 制作谷歌MD风格涟漪效果的Vue组件
34. vue-touch-keyboard- VueJS虚拟键盘组件
35. vue-parallax- 整洁的视觉效果
36. vue-typewriter- vue组件类型
37. vue-ios-alertview- iOS7+ 风格的alertview服务
38. paco-ui-vue- PACOUI的vue组件
39. vue-button- Vue按钮组件

#### **开发框架**

1. vue.js - 流行的轻量高效的前端组件化方案
2. vue-admin - Vue管理面板框架
3. electron-vue - Electron及VueJS快速启动样板
4. vue-2.0-boilerplate - Vue2单页应用样板
5. vue-webgulp - 仿VueJS Vue loader示例
6. vue-bulma - 轻量级高性能MVVM Admin UI框架
7. vue-spa-template - 前后端分离后的单页应用开发
8. Framework7-Vue - VueJS与Framework7结合
9. vue-element-starter - vue启动页

#### **实用库**

1. vuelidate - 简单轻量级的基于模块的Vue.js验证
2. qingcheng - qingcheng主题
3. vuex - 专为 Vue.js 应用程序开发的状态管理模式
4. vue-axios - 将axios整合到VueJS的封装
5. vue-desktop - 创建管理面板网站的UI库
6. vue-meta - 管理app的meta信息
7. avoriaz - VueJS测试实用工具库
8. vue-framework7 - 结合VueJS使用的Framework7组件
9. vue-lazy-render - 用于Vue组件的延迟渲染
10. vue-svg-icon - vue2的可变彩色svg图标方案
11. vue-online - reactive的在线和离线组件
12. vue-password-strength-meter - 交互式密码强度计
13. vuep - 用实时编辑和预览来渲染Vue组件
14. vue-bootstrap-modal - vue的Bootstrap样式组件
15. element-admin - 支持 vuecli 的 Element UI 的后台模板
16. vue-shortkey - 应用于Vue.js的Vue-ShortKey 插件
17. cleave - 基于cleave.js的Cleave组件
18. vue-events - 简化事件的VueJS插件
19. http-vue-loader - 从html及js环境加载vue文件
20. vue-electron - 将选择的API封装到Vue对象中的插件
21. vue-router-transition - 页面过渡插件
22. vuemit - 处理VueJS事件
23. vue-cordova - Cordova的VueJS插件
24. vue-qart - 用于qartjs的Vue2指令
25. vue-websocket - VueJS的Websocket插件
26. vue-gesture - VueJS的手势事件插件
27. vue-local-storage - 具有类型支持的Vuejs本地储存插件
28. lazy-vue - 懒加载图片
29. vue-lazyloadImg - 图片懒加载插件
30. vue-bus - VueJS的事件总线
31. vue-observe-visibility - 当元素在页面上可见或隐藏时检测
32. vue-notifications - 非阻塞通知库
33. v-media-query - vue中添加用于配合媒体查询的方法
34. vuex-shared-mutations - 分享某种Vuex mutations
35. vue-lazy-component - 懒加载组件或者元素的Vue指令
36. vue-reactive-storage - vue插件的Reactive层
37. vue-ts-loader - 在Vue装载机检查脚本
38. vue-pagination-2 - 简单通用的分页组件
39. vuex-i18n - 定位插件
40. Vue.resize - 检测HTML调整大小事件的vue指令
41. vue-zoombox - 一个高级zoombox
42. leo-vue-validator - 异步的表单验证组件
43. modal - Vue Bulma的modal组件
44. Famous-Vue - Famous库的vue组件
45. vue-input-autosize - 基于内容自动调整文本输入的大小
46. vue-file-base64 - 将文件转换为Base64的vue组件
47. Vue-Easy-Validator - 简单的表单验证
48. vue-truncate-filter - 截断字符串的VueJS过滤器

#### **服务端**

1. vue-ssr - 结合Express使用Vue2服务端渲染
2. nuxt.js - 用于服务器渲染Vue app的最小化框架
3. vue-ssr - 非常简单的VueJS服务器端渲染模板
4. vue-easy-renderer - Nodejs服务端渲染
5. express-vue - 简单的使用服务器端渲染vue.js



#### **辅助工具**

1. DejaVue - Vuejs可视化及压力测试
2. vue-generate-component - 轻松生成Vue js组件的CLI工具
3. vscode-VueHelper - 目前vscode最好的vue代码提示插件
4. vue-play - 展示Vue组件的最小化框架
5. VuejsStarterKit - vuejs starter套件
6. vue-multipage-cli - 简单的多页CLI

#### **应用实例**

1. pagekit - 轻量级的CMS建站系统
2. vuedo - 博客平台
3. koel - 基于网络的个人音频流媒体服务
4. CMS-of-Blog - 博客内容管理器
5. vue-cnode - 重写vue版cnode社区
6. vue-ghpages-blog - 依赖GitHub Pages无需本地生成的静态博客
7. swoole-vue-webim - Web版的聊天应用
8. fewords - 功能极其简单的笔记本
9. jackblog-vue - 个人博客系统
10. vue-blog - 使用Vue2.0 和Vuex的vue-blog
11. vue-dashing-js - nuvo-dashing-js的fork
12. rss-reader - 简单的rss阅读器

#### **Demo示例**

1. eleme - 高仿饿了么app商家详情
2. NeteaseCloudWebApp - 高仿网易云音乐的webapp
3. vue-zhihu-daily - 知乎日报 with Vuejs
4. Vue-cnodejs - 基于vue重写Cnodejs.org的webapp
5. vue2-demo - 从零构建vue2 + vue-router + vuex 开发环境
6. vue-wechat - vue.js开发微信app界面
7. vue-music - Vue 音乐搜索播放
8. maizuo - vue/vuex/redux仿卖座网
9. vue-demo - vue简易留言板
10. spa-starter-kit - 单页应用启动套件
11. zhihudaily-vue - 知乎日报web版
12. douban - 模仿豆瓣前端
13. vue-Meizi - vue最新实战项目
14. vue-demo-kugou - vuejs仿写酷狗音乐webapp
15. vue2.0-taopiaopiao - vue2.0与express构建淘票票页面
16. node-vue-server-webpack - Node.js+Vue.js+webpack快速开发框架
17. VueDemo_Sell_Eleme - Vue2高仿饿了么外卖平台
18. vue-leancloud-blog - 一个前后端完全分离的单页应用
19. vue-fis3 - 流行开源工具集成demo
20. mi-by-vue - VueJS仿小米官网
21. vue-demo-maizuo - 使用Vue2全家桶仿制卖座电影
22. vue2.x-douban - Vue2实现简易豆瓣电影webApp
23. vue-adminLte-vue-router - vue和adminLte整合应用
24. vue-zhihudaily - 知乎日报 Web 版本
25. Zhihu-Daily-Vue.js - Vuejs单页网页应用
26. vue-axios-github - 登录拦截登出功能
27. vue2.x-Cnode - 基于vue全家桶的Cnode社区
28. hello-vue-django - 使用带有Django的vuejs的样板项目
29. websocket_chat - 基于vue和websocket的多人在线聊天室
30. x-blog - 开源的个人blog项目
31. vue-cnode - vue单页应用demo
32. vue-express-mongodb - 简单的前后端分离案例
33. photoShare - 基于图片分享的社交平台
34. notepad - 本地存储的记事本
35. vue-zhihudaily-2.0 - 使用Vue2.0+vue-router+vuex创建的zhihudaily
36. vueBlog - 前后端分离博客
37. Zhihu_Daily - 基于Vue和Nodejs的Web单页应用
38. vue-ruby-china - VueJS框架搭建的rubychina平台
39. vue-koa-demo - 使用Vue2和Koa1的全栈demo
40. life-app-vue - 使用vue2完成多功能集合到小webapp
41. vue-trip - vue2做的出行webapp
42. github-explorer - 寻找最有趣的GitHub库
43. vue-ssr-boilerplate - 精简版的ofvue-hackernews-2
44. vue-bushishiren - 不是诗人应用
45. houtai - 基于vue和Element的后台管理系统
46. ios7-vue - 使用vue2.0 vue-router vuex模拟ios7
47. Framework7-VueJS - 使用移动框架的示例
48. cnode-vue - 基于vue和vue-router构建的cnodejs web网站SPA
49. vue-cli-multipage-bootstrap - 将vue官方在线示例整合到组件中
50. vue-cnode - 用 Vue 做的 CNode 官网
51. seeMusic - 跨平台云音乐播放器
52. HyaReader - 移动友好的阅读器
53. zhihu-daily - 轻松查看知乎日报内容
54. vue-cnode - 使用cNode社区提供的接口
55. zhihu-daily-vue - 知乎日报
56. vue-dropload - 用以测试下拉加载与简单路由
57. vue-cnode-mobile - 搭建cnode社区
58. Vuejs-SalePlatform - vuejs搭建的售卖平台demo
59. vue-memo - 用 vue写的记事本应用
60. sls-vuex2-demo - vuex2商城购物车demo
61. v-notes - 简单美观的记事本
62. vue-starter - VueJs项目的简单启动页

***
