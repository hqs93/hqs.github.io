---
title: Vue3.x项目搭建流程
categories: 
- [web前端, Vue3, Vue3.x项目搭建流程]
---

# Vue3.x项目搭建流程

<!-- more -->

### 环境准备

nvm:node版本管理工具

node:node环境



### 全局安装工具

yarn或cnpm:包管理工具

vue-cli:4.5.xx

webpack: 5.39.1

webpack-cli: 4.7.2

配置镜像源、配置环境变量



### 项目搭建

包管理工具优先 yarn、npm > cnpm

配置yarn镜像源,yarn中node-sass是国外镜像源,不配置很大概率下载失败.

```ruby
yarn config set sass_binary_site http://cdn.npm.taobao.org/dist/node-sass -g
```



### 环境配置

env文件
  开发环境（dev)：开发环境是程序猿们专门用于开发的服务器，配置可以比较随意，为了开发调试方便，一般打开全部错误报告。

  测试环境(test)：一般是克隆一份生产环境的配置，一个程序在测试环境工作不正常，那么肯定不能把它发布到生产机上。

  灰度环境（pre）：灰度环境，外部用户可以访问，但是服务器配置相对低，其它和生产一样。 <很多企业将test环境作为Pre环境 >

  生产环境（prod）：是值正式提供对外服务的，一般会关掉错误报告，打开错误日志

vue提供的默认环境配置

 1 普通全局变量 .env
 2 开发环境变量 .env.development
 3 生产环境变量 .env.production

package.json配置

```
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "serve:sit": "vue-cli-service serve --mode sit",
    "serve:prod": "vue-cli-service serve --mode prod",
    "build:sit": "vue-cli-service build --mode sit",
    "build:prod": "vue-cli-service build --mode prod"
  },
```

.env.xxx(test、prod)

```
NODE_ENV = "prod"
BASE_URL = "https://build-prod.com"
VUE_APP_PUBLIC_PATH = "../"
VUE_APP_API = "https://build-prod.com/api"

ACCESS_KEY_ID = "1111"
ACCESS_KEY_SECRET = "2222"
```

启动指令

```ruby
yarn serve:sit
npm run serve:sit
```



### 安装预处理器

如果在vue create中没有选择scss预处理,则单独下载,vue add 采用yarn安装插件,如果源有问题可更换为npm下载

```ruby
# yarn下载方式
vue add style-resources-loader

# npm下载方式
npm i style-resources-loader
npm i vue-cli-plugin-style-resources-loader
```



### 配置vue.config.js文件

配置项

```javascript
module.exports = {
  // style-resources-loader,css处理器资源加载器,先安装预处理器(less、scss/sass)再安装资源加载器(style-resources-loader)
  pluginOptions: {
    'style-resources-loader': {
      preProcessor: 'scss',
      patterns: []
    }
  },
  // 配置ESlint自动格式化
  chainWebpack: config => {
    config.module
      .rule('eslint')
      .use('eslint-loader')
      .loader('eslint-loader')
      .tap(options => {
        options.fix = true
        return options
      })
  }
}
```



```javascript
const path = require('path')

module.exports = {  
  // 部署应用包时的基本 URL,用法和 webpack 本身的 output.publicPath 一致
  publicPath: './',  
  // 输出文件目录
  outputDir: 'dist',  
  // eslint-loader 是否在保存的时候检查
  lintOnSave: true,  
  // 是否使用包含运行时编译器的 Vue 构建版本
  runtimeCompiler: false,  
  // 生产环境是否生成 sourceMap 文件
  productionSourceMap: false,  
  // 生成的 HTML 中的 <link rel="stylesheet"> 和 <script> 标签上启用 Subresource Integrity (SRI)
  integrity: false,  
  // webpack相关配置
  chainWebpack: (config) => {
    config.resolve.alias
      .set('vue$', 'vue/dist/vue.esm.js')
      .set('@', path.resolve(__dirname, './src'))
  },
  configureWebpack: (config) => {    
  if (process.env.NODE_ENV === 'production') {      
      // 生产环境
      config.mode = 'production'
    } else {      
      // 开发环境
      config.mode = 'development'
    }
  },  
  // css相关配置
  css: {    
    // 是否分离css（插件ExtractTextPlugin）
    extract: true,    
    // 是否开启 CSS source maps
    sourceMap: false,   
    // css预设器配置项
    loaderOptions: {},    
    // 是否启用 CSS modules for all css / pre-processor files.
    modules: false
  },  
  // 是否使用 thread-loader
  parallel: require('os').cpus().length > 1, 
  // PWA 插件相关配置
  pwa: {}, 
  // webpack-dev-server 相关配置
  devServer: {
    open: true,
    host: 'localhost',
    port: 8080,
    https: false,
    hotOnly: false,   
    // http 代理配置
    proxy: {      
      '/api': {
        target: 'http://127.0.0.1:3000/api',
        changeOrigin: true,
        pathRewrite: {          
            '^/api': ''
        }
      }
    },
    before: (app) => {}
  }, 
  // 第三方插件配置
  pluginOptions: {

  }
}
```



### 封装svg组件

1.创建@src/assets/icons文件夹

@src/assets/icons/svg存放svg标签文件



2.创建@src/components/SvgIcon组件

Ts写法可配合vue-property-decorator插件

@src/components/SvgIcon/index.vue封装svg组件

```JavaScript
<template>
  <svg :class="svgClass" aria-hidden="true" @mousedown="clickIcon">
    <use :xlink:href="iconName"/>
  </svg>
</template>

<script>
export default {
  name: 'SvgIcon',
  props: {
    iconClass: {
      type: String,
      required: true
    },
    className: {
      type: String,
      default: ''
    }
  },
  computed: {
    iconName () {
      return `#icon-${this.iconClass}`
    },
    svgClass () {
      if (this.className) {
        return 'svg-icon ' + this.className
      } else {
        return 'svg-icon'
      }
    }
  }
}
</script>

<style scoped>
  .svg-icon {
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }
</style>
```



vue2可以创建src/icons/index.js组件进行svg文件导入

vue3需要在main.js中引入svg

```JavaScript
import { createApp } from 'vue'
import 'normalize.css/normalize.css'
import ElementPlus from 'element-plus'
import locale from 'element-plus/lib/locale/lang/zh-cn'
import 'element-plus/lib/theme-chalk/index.css'
import './styles/element-variables.scss'
import App from './App.vue'
import store from './store'
import router from './router'
import SvgIcon from '@/components/SvgIcon/index.vue'

createApp(App)
  .use(store)
  .use(ElementPlus, { locale })
  .use(router)
  .component('svg-icon', SvgIcon) // 全局挂载
  .mount('#app')

// 导入svg
const req = require.context('./assets/icons/svg', false, /\.svg$/)
const requireAll = requireContext => requireContext.keys().map(requireContext)
requireAll(req)

```



下载svg-sprite-loader插件

它是一个 webpack loader ，可以将多个 svg 打包成 `svg-sprite` .

我们发现`vue-cli`默认情况下会使用 `url-loader` 对svg进行处理，会将它放在`/img` 目录下,所以这时候我们引入`svg-sprite-loader` 会引发一些冲突.

```ruby
npm install svg-sprite-loader --save-dev
```

配置vue.config.js

```javascript
// 链式配置
chainWebpack: config => {
    // svg
    const svgRule = config.module.rule('svg')
    svgRule.uses.clear()
    svgRule.exclude.add(/node_modules/)
    svgRule
      .test(/\.svg$/)
      .use('svg-sprite-loader')
      .loader('svg-sprite-loader')
      .options({
        symbolId: 'icon-[name]'
      })
    const imagesRule = config.module.rule('images')
 	imagesRule.exclude.add(resolve('src/assets/icons/svg'))
  	config.module.rule('images').test(/\.(png|jpe?g|gif|svg)(\?.*)?$/)
}
```



使用

```javascript
<svg-icon icon-class="eye" className="icon"></svg-icon>
```



### 组件结构

components是小组件
containers是容器级组件（根据项目大小决定是否使用）
views是页面级组件

也可以根据项目结构,小组件放置在页面级组件中或者容器级组件,缺点是其他页面组件调用时路径混乱,不好维护.



### 封装vuex

store/index.js

```javascript
import { createStore } from 'vuex'
import getters from './getters'
// node方法context(),获取modules文件下的所有模块进行批量挂载
const modulesFiles = require.context('./modules', true, /\.js$/)
const modules = modulesFiles.keys().reduce((module, modulePath) => {
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  module[moduleName] = value.default
  return module
}, {})

const store = {
  modules,
  getters
}

export default createStore(store)
```

store/getters.js

```javascript
// 简单理解,getters是store的计算属性
const getters = {
  token: state => state.user.token,
  xxx: state => state.xxx.xxx,
  xxx: state => state.xxx.xxx
}
export default getters
```



### 去除console

1.babel-plugin-transform-remove-console插件,建议配置

Babel.config.js

```javascript
const plugins = []
if (process.env.NODE_ENV === 'production') {
  plugins.push('transform-remove-console')
}

module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset'
  ],
  plugins
}
```



2.uglifyjs-webpack-plugin插件,不建议会发生依赖报错

安装依赖

```ruby
npm install uglifyjs-webpack-plugin --save-dev
```

webpack配置

```javascript
chainWebpack: (config)=>{
      // 根据环境去除console
      config
        .plugin('uglifyjs-plugin')
        .use('uglifyjs-webpack-plugin', [{
          uglifyOptions: {
            warnings: false,
            compress: {
              drop_console: true,
              drop_debugger: false,
              pure_funcs: ['console.log']
            }
          }
        }])
  }
```



### sass全局变量

导入scss、sass等css预处理器文件来实现自定义功能时,配合sass-resources-loader插件就不用单独引入scss文件.

安装sass-resources-loader

```javascript
npm install sass-resources-loader --save-dev
```

配置

```javascript
// vue.config.js
module.exports = {
  chainWebpack: config => {
    const oneOfsMap = config.module.rule('scss').oneOfs.store
    oneOfsMap.forEach(item => {
      item
        .use('sass-resources-loader')
        .loader('sass-resources-loader')
        .options({
          // Provide path to the file with resources
          resources: './path/to/resources.scss',

          // Or array of paths
          resources: ['./path/to/vars.scss', './path/to/mixins.scss']
        })
        .end()
    })
  }
}
```

使用

```javascript
// home.vue
<style lang="scss" scoped>
    .search{
        font-size: 12px;
        color: $main-color;
    }
</style>
```



### 引入CDN源

先分离打包的外部依赖,然后配置cdn地址

```javascript
module.exports = {
  chainWebpack: config => {
    // 打包时分离外部依赖
    config.externals = {
      vue: 'Vue',
      'element-plus': 'ElementPlus',
      'vue-router': 'VueRouter',
      vuex: 'Vuex',
      axios: 'axios'
    }

    // 引入cdn,如果使用多页面打包，使用vue inspect --plugins查看html是否在结果数组中
    config.plugin('html').tap(args => {
      // html中添加cdn
      args[0].cdn = {
        // css: ['//cdn.bootcdn.net/ajax/libs/element-plus/1.0.2-beta.48/packages/theme-chalk/src/index.scss'],
        js: [
          '//cdn.bootcdn.net/ajax/libs/vue/3.0.0/vue.cjs.min.js', // 访问https://cdn.bootcdn.net获取最新版本
          '//cdn.bootcdn.net/ajax/libs/vue-router/4.0.0/vue-router.cjs.min.js',
          '//cdn.bootcdn.net/ajax/libs/vuex/4.0.0/vuex.cjs.min.js',
          '//cdn.bootcdn.net/ajax/libs/axios/0.21.1/axios.min.js',
          '//cdn.bootcdn.net/ajax/libs/element-plus/1.0.2-beta.48/index.esm.min.js'
        ]
      }
      return args
    })
	}
}
```



### 在vue-cli3以上设置浏览器图标

在vue-cli3以上的版本里设置浏览器图标

1.第一步 需要现在vue.config.js里面这只一个paw插件，设置代码如下：

```
module.exports = {
　　pwa: {
  　　iconPaths: {
   　　favicon32: 'favicon.ico',
   　　favicon16: 'favicon.ico',
   　　appleTouchIcon: 'favicon.ico',
   　　maskIcon: 'favicon.ico',
   　　msTileImage: 'favicon.ico'
  　　}
 　}
}
```

2.第二部 在pulic文件夹里index.html(入口文件有可能在根目录每个项目不一样)文件中引入图标文件

3.link标签引入

```html
<link rel="icon" type="image/x-icon" href="<%= BASE_URL %>favicon.ico">
```

**4.根据项目部署的方式(publicPath)决定图标访问的路径(iconPaths)是绝对路径(/)还是相对路径(../).**



### vue3小记

vue3中的template标签与vue2中的一样使用,唯一的差别是vue3的template标签并不要求有一个根元素,可以有多个根元素



### 常用插件

Vue3DraggableResizable: 拖拉拽插件

axiosRetry: Axios 插件 重试失败的请求
