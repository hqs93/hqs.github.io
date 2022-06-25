---
title: Vue2.x项目搭建流程
categories: 
- [web前端, Vue2, Vue2.x项目搭建流程]
---

# Vue2.x + webpack4 配置手册

<!-- more -->

>开发环境:

 - 安装nvm或n版本管理工具,安装node环境,全局安装cnpm,全局安装npm-check检查过期、错误、无用的依赖
 - 配置nvm、node、npm环境变量,确认终端正常运行工具指令
 - 全局安装webpack和webpack-cli
 - 全局安装vue-cli
 - 确认工具成功安装 -v、-V、--version查看版本
 - 查阅官方文档确认环境、工具、插件的版本兼容性和版本冲突,使用@指定兼容版本下载
 - 由于nodejs早期版本管理混乱,导致基于nodejs开发的工具都存在兼容性问题,主要以v8.0~、v10.0~、v12.0~、v14.0~这几个版本差异较大.  

>版本说明:

|  工具   | 版本  |
|  ----  | ----  |
| node  | v14.15.4 |
| npm  | 6.14.11 |
| webpack/webpack-cli  | 5.23.0/4.5.0 |
| vue/cli  | 4.5.10 |

***

## 一.创建项目

### 1. 基于webpack模版创建vue项目
```ruby
vue init webpack my-project
```

### 2. 创建成功后的目录说明
```ruby
- build # webpack配置文件
  -- build.js # 使用webpack打包时的入口文件
  -- check-versions.js # 用于版本node和npm版本的检测
  -- utils.js # 用于项目中关于loader的引用和项目title、icon等设置
  -- vue-loader.conf.js # 项目是基于vue的,需要vue-loader来识别,vue后缀的文件,vue-loader的配置文件
  -- webpack.base.conf.js # webpack的开发环境和生产环境的共有配置(开发环境和生产环境都是需要执行的配置)
  -- webpack.dev.conf.js # webpack的开发环境的特有配置(只在开发环境中执行,生产环境中不执行)
  -- webpack.prod.conf.js # webpack的生产环境的特有配置(只在生产环境中执行,开发环境中不执行)
- config # webpack配置文件
  -- dev.env.js # 用于配置项目的环境变量
  -- prod.env.js # 用于配置项目的环境变量
  -- index.js # 用于webpack的一些配置信息
- src
  -- assets # 模块静态资源,css、js、图片、字体等
  -- components # 模块化
  -- router # 路由
  -- store # 基于webpack创建的vue项目没有vuex配置选项,自行创建vuex文件
  -- App.vue # 根组件
  -- main.js # 作为webpack项目的入口文件
- static # 整体项目的静态资源,图片、字体等
- .babelrc # babel的配置文件,配置转码的规则以及插件
- .editorconfig # 编辑项目代码风格
- .eslintrc.js # eslint配置文件,检查js代码风格
- .gitignore # 忽略配置文件
- .npmrc # npm下载源配置,自行创建
- .postcssrc.js # 处理CSS前缀问题,与Sass,Stylus或Less等预处理器共通使用
```

### 3. 下载源配置
配置项目下载源,除了npm下载源还可以配置依赖包的私有下载源,有些公司会有自己的私有镜像仓库
创建.npmrc文件
```javascript
home="https://npm.taobao.org" // 指定默认下载源
registry="https://registry.npm.taobao.org/" // 指定npm下载源
sass_binary_site="https://npm.taobao.org/mirrors/node-sass/" // 指定sass下载源
phantomjs_cdnurl="http://cnpmjs.org/downloads" // 指定phantomjs下载源,SEO优化方案针对爬虫的处理
electron_mirror="https://npm.taobao.org/mirrors/electron/" // 指定electron下载源,跨平台桌面应用开发工具
sqlite3_binary_host_mirror="https://foxgis.oss-cn-shanghai.aliyuncs.com/" // 指定sqlite3下载源,sqlite3数据库
profiler_binary_host_mirror="https://npm.taobao.org/mirrors/node-inspector/"
chromedriver_cdnurl="https://cdn.npm.taobao.org/dist/chromedriver" // 指定chromedriver下载源,用于e2e测试
```

### 4. 代码规范配置
 - .editorconfig
    编辑器配置,用于覆盖编辑器默认配置,以确保不同编辑器之间代码格式的统一.

 - .prettier
    根据规范,自动格式化代码.```vscode编辑器需要自行安装prettier插件```
   
 - .eslintrc.js
    制定团队代码规范.```vscode编辑器需要自行安装editorconfig插件```.

***

## 二.适配方案

### 1.rem适配方案

|  插件   | 说明  |
|  ----  | ----  |
| ***amfe/lib-flexible***  | 可伸缩布局方案,自动设置根节点字体大小,较早的lib-flexible已经停止更新,故而采用amfe-flexible.**关于1px转化问题： 1px转化rem后有些手机上显示模糊或不显示,解决办法：将px大写,即1px写为1PX** |
| ***px2rem-loader***  | 将px转换成rem,配合flexible插件使用. |
| ***PostCss***  | **postcss-px2rem-exclude(推荐)**、**postcss-pxtorem**、**postcss-px2rem**,PostCSS系列是使用JS插件来转换CSS的工具,支持变量,混入,未来CSS语法,内联图像等等.内置的重要插件Autoprefixer自动前缀解决各大浏览器兼容问题.这些插件与webpack集成能解决大多数兼容和适配问题,提高开发效率. |
 - 此类插件一般放在开发依赖中(devDependencies),下载指令中需添加 --save-dev 指令
```ruby
npm install amfe-flexible --save-dev
npm install px2rem-loader --save-dev
npm install postcss-px2rem-exclude --save-dev
```
 - 配置项可查阅文档或自行百度.
 - 适配大屏、pc端、移动端,较为方便.

### 2. vw适配方案

|  插件   | 说明  |
|  ----  | ----  |
| ***postcss-import***  | 主要功有是解决@import引入路径问题.使用这个插件,可以很轻易的使用本地文件、node_modules或者web_modules的文件.这个插件配合postcss-url使引入文件变得更轻松. |
| ***postcss-url***  | 主要用来处理文件,比如图片文件、字体文件等引用路径的处理. |
| ***postcss-px-to-viewport***  | 主要用来把px单位转换为vw、vh、vmin或者vmax这样的视窗单位,也是vw适配方案的核心插件之一. |
| ***postcss-viewport-units***  | 主要是给CSS的属性添加content的属性,给vw、vh、vmin和vmax做适配的操作,这是实现vw布局必不可少的一个插件. |
| ***postcss-cssnext***  | 该插件可以让我们使用CSS未来的特性,其会对这些特性做相关的兼容性处理. |
| ***cssnano***  | 主要用来压缩和清理CSS代码.在Webpack中,cssnano和css-loader捆绑在一起,所以不需要自己加载它. |
| ***postcss-write-svg***  | 主要用来处理移动端1px的解决方案. |
| ***postcss-aspect-ratio-mini***  | 主要用来处理元素容器宽高比. |

#### 1.安装相关依赖

```shell
npm i postcss-aspect-ratio-mini postcss-px-to-viewport postcss-write-svg postcss-cssnext postcss-viewport-units cssnano cssnano-preset-advanced --S   
```

#### 2.配置.postcssrc.js

```javascript
module.exports = {
  "plugins": {
    "postcss-import": {},
    "postcss-url": {},
    "postcss-aspect-ratio-mini": {}, 
    "postcss-write-svg": { utf8: false }, "postcss-cssnext": {}, "postcss-px-to-viewport": { 
      viewportWidth: 750, // (Number) 视口宽度. 
      viewportHeight: 1334, // (Number) 视口高度. 
      unitPrecision: 3, // (Number) 允许rem单位有多少位小数. 
      viewportUnit: 'vw', // (String) 视口单位.
      selectorBlackList: ['.ignore', '.hairlines'], // (Array) 要忽略的选择器,忽略后保留px. 
      minPixelValue: 1, // (Number) 设置要替换的最小像素值.
      mediaQuery: false // (Boolean) 允许在媒体查询中转换px. 
     },
    "postcss-viewport-units":{}, 
    "cssnano": { 
      preset: "advanced", 
      autoprefixer: false,
      "postcss-zindex": false
      }
    // to edit target browsers: use "browserslist" field in package.json
  }
}
```

#### **需要注意的点：**

1.全局添加img的content，避免部分浏览器，图片不显示！

```css
img{
   content:'normal'!important;
}
```

2.对于伪元素(:before,:after)的影响，要么添加新元素来设置，或者，在content后添加！important；

3.针对1px的解决方案，安装 npm i postcss-write-svg -S

```css
第一种：border-image
@svg 1px-border { 
     height: 2px;
    @rect { fill: var(--color, black); width: 100%; height: 50%; }
 } 
.example {
    border: 1px solid transparent;
    border-image: svg(1px-border param(--color #00b1ff)) 2 2 stretch; 
}
第二种：background-image
@svg square {
   @rect { fill: var(--color, black);
      width: 100%;
      height: 100%; 
  } 
 } 
.example { background: white svg(square param(--color #00b1ff)); }
```

4.记得添加头部meta标签

```html
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,minimum-scale=1,user-scalable=no" />
```

   与flexible适配方案相比，VW是浏览器客户端原生支持的特性，不需要依赖js来做任何的判断和计算。而flexible也是来源于Viewport单位的思路。通过JS来判断，动态修改meta的值。



***

## 三.CSS解决方案

### 1. 样式初始化
|  插件   | 说明  | 所需依赖 |
|  ----  | ----  | ---- |
|  ***normalize.css***  | 统一了基本的样式.  | ---- |
|  ***postcss-loader & Autoprefixer***  | 这是postcss系列插件(Autoprefixer在以前是postcss系列的核心功能),postcss-loader负责处理css解析让webpack能够识别,Autoprefixer负责自动化添加浏览器前缀,两者结合使用解决大多数浏览器的兼容问题.  | **postcss-loader**、**Autoprefixer** |

### 2. CSS预处理器
 建议三选一,不建议组合使用,因为这三种预处理器百分之七八十的功能是类似的.
|  插件   | 说明  | 所需依赖 |
|  ----  | ----  | ---- |
|  ***sass***  | 使用最多的css预处理器,大而全. | **sass-loader**、**node-sass**、**vue-style-loader** |
|  ***stylus***  | 语法非常灵活,可以写出非常简洁的代码,但对使用团队的开发素养要求也更高,更需要有良好的开发规范或约定. | **less-loader**、**less** |
|  ***less***  | 比Stylus语义更清晰,比Sass更接近CSS语法,比较容易上手.但开发类库复杂,实现的代码会比较脏,能实现的功能也会受到DSL(领域特定语言)的制约. | **stylus-loader**、**stylus** |

***

## 四.区分环境

### 1. 为什么要区分环境?

 - 实际开发中会根据发布模式(A/B测试、蓝绿部署、滚动更新、灰度发布)来配置相对应的环境,后台是根据环境来加载对应的配置文件,同样前端代码也需要根据环境加载对应的配置文件,不管是哪种发布模式都有其适用的场景,但对于开发来讲区分环境是必须的,在开发的过程中会遇到各种问题,区分环境的目的就是为了让开发人员在开发的过程中可以方便调试,保持高效的开发,同时也要及时解决生产环境上发生的突发情况,保证生产环境的安全,总结一句话:开发是必需,上线是目的.

 - 相关的配置文件
```ruby
- build # webpack配置文件
  -- build.js # 使用webpack打包时的入口文件
  -- check-versions.js # 用于版本node和npm版本的检测
  -- utils.js # 用于项目中关于loader的引用和项目title、icon等设置
  -- vue-loader.conf.js # 项目是基于vue的,需要vue-loader来识别,vue后缀的文件,vue-loader的配置文件
  -- webpack.base.conf.js # webpack的开发环境和生产环境的共有配置(开发环境和生产环境都是需要执行的配置)
  -- webpack.dev.conf.js # webpack的开发环境的特有配置(只在开发环境中执行,生产环境中不执行)
  -- webpack.prod.conf.js # webpack的生产环境的特有配置(只在生产环境中执行,开发环境中不执行)
- config # webpack配置文件
  -- dev.env.js # 用于配置项目的环境变量
  -- prod.env.js # 用于配置项目的环境变量
  -- index.js # 用于webpack的一些配置信息
```

### 2. 描述文件
 - package.json文件就是项目描述文件,是nodejs为项目生成的配置描述文件

 - 两个核心中间件的介绍 **cross-env** 和 **webpack-dev-server**
| 中间件 | 作用 |
| ---- | ---- |
| **cross-env** | **可以跨平台的设置及使用环境变量,不需要再全局配置NODE_ENV变量,在script脚本中修改NODE_ENV值从而实现不同环境中,proccess.env.NODE_ENV的不同** |
| **webpack-dev-server** | **webpack官方提供的一个小型Express服务器,使用它可以为webpack打包生成的资源文件提供web服务.主要提供两个功能: 1.为静态文件提供服务、2.自动刷新和热替换(HMR、热部署)** |

### 3. 通过自定义命令行加载自定义配置
  process对象是node的全局变量,通过修改、增加全局变量来实现自定义命令行
   - 添加自定义命令行
```json
"scripts": {
  "dev:sit": "cross-env NODE_ENV=development webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
  "dev:prod": "cross-env NODE_ENV=development webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
  "start": "npm run dev",
  "lint": "eslint --ext .js,.vue src",
  "build:dev": "cross-env NODE_ENV=production env_config=dev node build/build.js",
  "build:prod": "cross-env NODE_ENV=production env_config=prod node build/build.js"
}
```

 - 在 webpack.DefinePlugin 实例中赋值自定义变量,```process.env.NODE_ENV默认production```
```javascript
// webpack.dev.js
new webpack.DefinePlugin({
  devSit: JSON.stringify(process.env.ENV_NODE_BUILD) // 第一种方式,直接赋值自定义变量名
  process.env: require('../config/dev.env') // 第二种方式,结合 config 文件下的变量配置文件,自定义环境变量存储(推荐)
})
```

 - 使用 **merge** 中间件合并环境变量配置,单双引号解决报错问题
```javascript
// dev.env.js 文件
'use strict'
const merge = require('webpack-merge')
const prodEnv = require('./prod.env')

module.exports = merge(prodEnv, (function(devEnv){
  const optionEnv = {
    devSit:{
      NODE_ENV: '"development"',
      ENV_NODE_BUILD:'"devSit"'
    },
    devProd: {
      NODE_ENV: '"development"',
      ENV_NODE_BUILD:'"devProd"'
    }
  }
  return optionEnv[devEnv]
})(process.env.ENV_NODE_BUILD))
```

***

## 五.配置代理

***

## 六.性能优化

### 1. 配置别名优化超长引入路径

 - 通过配置别名,能够优化导入、运行、打包速度,并且省略引入路径时的冗长代码,定义常用的路径一般约定以@开头,也可以自定义
```javascript
// webpack.base.conf.js文件
module.exports = {
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      "@assets": "src/assets",
      "@scss": "src/assets/scss",
      "@components": "src/components",
      "@plugins": "src/plugins",
      "@views": "src/views",
      "@router": "src/router",
      "@store": "src/store",
      "@layouts": "src/layouts",
      "@static": "src/static",
      moment: "moment/min/moment-with-locales.min.js"
    }
  }
}
```

 - 在引入时直接使用别名即可
```JavaScript
require('moment') // 通过别名引入插件
import App from '@store/images/1.png'
```

***

### 2.SourceMap 的配置和优化

 - 什么是 Sourcemap ?
Sourcemap 本质上是一个信息文件,里面储存着代码转换前后的对应位置信息.它记录了转换压缩后的代码所对应的转换前的源代码位置,是源代码和生产代码的映射. Sourcemap 解决了在打包过程中,代码经过压缩,去空格以及 babel 编译转化后,由于代码之间差异性过大,造成无法debug的问题.

 - Sourcemap 的作用
简单说 Sourcemap 构建了处理前以及处理后的代码之间的一座桥梁,方便定位生产环境中出现 bug 的位置.因为现在的前端开发都是模块化、组件化的方式,在上线前对 js 和 css 文件进行合并压缩容易造成混淆.如果对这样的线上代码进行调试,肯定不切实际,sourceMap 的作用就是能够让浏览器的调试面版将生成后的代码映射到源码文件当中,开发者可以在源码文件中 debug,这样就会让程序员调试轻松、简单很多.

 - Sourcemap 的用法
Sourcemap 的种类有很多, 在生产环境下可以用process.env判断一下.webpack中可以在devtool中设置,在开发环境中可以配置devtool为cheap-module-source-map,方便调试.生产环境下建议采用none的方式,这样做不暴露源代码.或者是 nosources-source-map 的方式,既可以定位源代码位置,又不暴露源代码.

### 3.css、js文件压缩
 - optimize-css-assets-webpack-plugin:是一个优化和压缩css资源的插件
 - terser-webpack-plugin:是一个优化和压缩JS资源的插件
 - extract-text-webpack-plugin: 背景是在webpack打包的时候,会把css,less、sass、image等等都打包到js文件中(**js、css都属于渲染阻塞资源,需要完全被解析完毕之后才能进入生成渲染树的环节**),而这会导致一些bug.样式是通过js 动态添加 style 标签,所以css动画会出现不必要的闪烁效果,css的stlye应该先被渲染.所以要把css抽离出来.那么就引入了extract-text-webpack-plugin这个插件.

### 4.Webpack中忽略对已知文件的解析
 - module.noParse 是 webpack 的还有一个非常实用的配置项,假设你 确定一个模块中没有其他新的依赖 就能够配置这项,webpack 将不再扫描这个文件里的依赖.
```javascript
module: {
  noParse: [/moment-with-locales/]
}
```

### 5.Webpack 中使用公用 CDN
 - externals声明一个外部依赖
```javascript
externals: {
  moment: true
}
```
 - html代码中加入CDN资源路径
```html
<script src="//apps.bdimg.com/libs/moment/2.8.3/moment-with-locales.min.js"></script>
```

### 6.去除console.log

### 7.单独打包第三方模块

### 8.Gzip压缩模式

### 9.Tree Shaking 删除冗余代码

### 10.按需加载

### 11.HappyPack 并行打包

### 12.文件和图片的处理

***

## 七. 常见问题

### 1. 静态资源路径配置问题
assetsPublicPath是配置为相对路径还是绝对路径,取决于你打包后前端资源怎么发布部署,**如果 index.html 以及 static 文件夹是直接放到容器的根目录,即访问路径为：http://xxxx:9090/index.html这种形式,那么直接使用“/”绝对路径**,如果说项目是在某个文件夹下或者合并到后台项目中去发布,即访问路径可能为：http://xxx:9090/projectname/index.html,那么就一定要使用'./',否则资源信息会找不到.

### 2. 项目打包编译时出现查找不到模块的问题
项目打包编译时出现查找不到模块的报错,导致这种情况发生的因素很多,可以按以下步骤排查.
1.首先要排查文件导入的路径问题,确定目录正确

2.然后排查文件编译问题,要让webpack成功编译文件,首先要确保webpack能识别文件,下载对应的webpack插件解析文件如css下载css-loader,sacc下载sass-loader等等

3.还有最容易被忽视的情况,也和编写代码的习惯有关,当前常见的开发环境Linux、Windows、Mac等等,其中Linux是严格区分字母大小写和符号的,所以在Windows和Mac中能成功编译打包不代表能在Linux系统中成功,当然前提是你能保持良好的编写习惯.文件名的命名和代码的编写都严格区分大小写或者遵守驼峰法,而这样做的好处是毋庸置疑的,因为大多数运维平台都是基于Linux的,这里就不讨论为什么要基于Linux了,作为开发首先要确保你编写的代码能在其他主流系统中正常运作,而不是只适应自己的系统.

***

## 八.JavaScript解决方案

## 九.热部署

## 十.Html解决方案

## 十一.SVG、Font字体、雪碧图等静态资源的解决方案

## 十二.多页面打包

## 十三.语法检测

## 十四.Docker容器化部署