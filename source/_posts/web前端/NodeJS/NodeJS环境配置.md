---
title: NodeJS 环境配置
categories: 
- [web前端, NodeJS, NodeJS 环境配置]
---

# Node.js

<!-- more -->

>  注解:

Node是javascript的开源运行时环境.它是一个跨平台环境，提供对Mac,Windows和Linux的支持.

早期node版本非常混乱，但兼容性基本是跟着ES标准走的，所以几个版本之间除了修复bug没有特别需要关注的东西,也就是ES6的支持程度不同.如果出现编译(node-sass)等报错,那么大概率是对ES6的编译和开发环境的兼容问题(node,window,linux),建议首先安装nvm版本管理工具对node进行管理,来解决环境问题.

> 注意项:

开发时的操作系统中,Linux严格区分大小写,Windows和Mac默认不区分大小写,所以在编译或者打包环节中,如果不严格执行驼峰写法,在Windows和Mac操作系统中可能不会有任何问题,但在Linux中就会出现因为大小写无法找到文件的问题.



## nvm和n

在node的版本管理工具中,最主流的方案有两个,nvm和n,两个工具的运作机制和特性有所不同,所以选择适合自己开发习惯的就行.

- nvm

nvm不是一个npm package,而是一个独立软件包,这意味着我们需要单独使用它的安装逻辑,nvm将不同的node版本存储在~/.nvm/下然后修改 $PATH,将指定版本的 node 路径加入,这样我们调用的 node 命令即是使用指定版本的 node.

nvm 显然比 n 要复杂一些,但是另一方面,由于它是一个独立软件包,因此它和 node 之间的关系看上去更合乎逻辑: nvm 不依赖 node 环境,是 node 依赖 nvm；而不像 n 那样产生类似循环依赖的问题

- n

n 是一个需要全局安装的 npm package, npm install -g n 这意味着,我们在使用 n 管理 node 版本前,首先需要一个 node 环境.我们或者用 Homebrew 来安装一个 node,或者从官网下载 pkg 来安装,总之我们得先自己装一个 node —— n 本身是没法给你装的,n是依赖于node环境的.

然后我们可以使用 n 来安装不同版本的 node

- nvm和n的选择

nvm 和 n 的差异还是比较大的,具体体现在: 安装简易度,nvm 安装起来显然是要麻烦不少,n 这种安装方式更符合 node 的惯性思维.
***对全局模块的管理,n 对全局模块毫无作为,因此有可能在切换了 node 版本后发生全局模块执行出错的问题,nvm 的全局模块存在于各自版本的沙箱中，切换版本后需要重新安装,不同版本间也不存在任何冲突.***
这里给出大体的建议:
如果你会频繁切换 node 版本（比如本地经常测试最新版的特性,同时又要兼顾代码在生产环境的兼容性）,那么从全局模块兼容性的角度考虑,只能使用 nvm.
如果你是一个轻量级的用户,不需要担心兼容性的问题,更关心 node 安装和使用上的体验,那么选择 n.

***



## 一.Node.js-Mac

>  注解:

MAC系统通常会使用homebrew来下载安装软件,使用 brew install nvm 可以直接下载nvm版本管理工具,但是由于brew安装nvm自身的bug(官方文档有说明可以查阅),在.nvm文件中缺少nvm-exec,nvm.sh两个文件会导致配置环境变量后终端依然无法识别nvm指令,所以这里不要使用homebrew的brew指令下载.



### 1.安装

mac安装nvm不推荐brew,这里使用官方文档的安装步骤.

#### 1.1 网络设置

使用vpn代理软件,如果没有就配置本机hosts.

- 配置hosts

将github仓库地址域名复制到https://www.ipaddress.com/网站,查询IP地址199.232.68.133

```ruby
cd ~ # 操作系统根目录
sudo open /etc/hosts # 找到hosts文件
199.232.68.133 raw.githubusercontent.com<br> # 末尾新增
```

#### 1.2 安装nvm或n版本管理工具.

- 安装前删除系统中存在的node

```ruby
brew remove --force node
sudo rm -r /usr/local/lib/node_modules
brew prune
sudo rm -r /usr/local/include/node
```

- 安装 nvm， 打开[nvm github官方文档](https://github.com/nvm-sh/nvm/blob/master/README.md#macos-troubleshooting)，查看最新版本

```ruby
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.36.0/install.sh | bash
```

- nvm -v 测试是否安装成功

#### 1.3 管理使用node版本

```ruby
nvm ls # 列出当前安装的所有node版本
nvm ls -remote # 列出所有node版本
nvm install <版本号> # 安装指定版本的node 版本号取nvm ls-remote列出的
nvm uninstall <版本号>  # 卸载指定版本号
nvm use <版本号> # 切换到指定node版本
```

#### 1.4 设置默认node版本

- 通过以上方式安装nvm后，切换好node版本，但是每次打开vscode后，版本号会变掉，这个时候可以设置默认版本

```ruby
nvm alias default <版本号>
```



### 2. 配置环境变量

Mac 默认用户环境变量配置文件为 ~/.bash_profile,Linux为~/.bashrc,

```ruby
sudo open ~/.bash_profile # 找到变量配置文件
source ~/.bash_profile # 保持修改
# 添加环境变量

# 配置淘宝镜像源

```

- .bash_profile文件中添加环境变量和淘宝镜像

```ruby
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

export NVM_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node
export NVM_IOJS_ORG_MIRROR=http://npm.taobao.org/mirrors/iojs
```

***



## 二.Node.js-windows7-11

### 1.安装

#### 1.1 安装程序

下载nvm安装程序根据步骤安装,**但是要注意nvm安装目录要放在磁盘的根目录，比如F:\nvm。否则会报错**

```ruby
exit status 1:乱码
```

#### 1.2 管理使用node版本

```ruby
nvm ls # 列出当前安装的所有node版本
nvm ls available # 列出所有node版本
nvm install <版本号> # 安装指定版本的node 版本号取nvm ls-remote列出的
nvm uninstall <版本号>  # 卸载指定版本号
nvm use <版本号> # 切换到指定node版本
```



### 2. 配置环境变量

nvm安装包会自动配置环境变量,只需关注NVM_HOME,NVM_SYMLINK这两组环境变量与settings.txt配置文件中的root: 和path: 相对应.

cnpm工具则需要单独配置环境变量,推荐yarn包管理工具

```ruby
变量名: CNPM_PATH
变量值: C:\nvm\v16.13.0\node_modules\cnpm\bin
```

node的插件或工具在nvm中是根据版本号配置的,如果有多个node版本,那么每个node版本都需要重新下载并配置.



### 3.Set-ExecutionPolicy脚本安全策略

- window系统的Set-ExecutionPolicy安全策略,用途是设定 powershell 脚本的安全策略。即决定什么 .ps 脚本可以被运行，什么脚本不允许运行。windows系统默认禁止运行脚本.

- RemoteSigned 参数要求所有网络下载的脚本和配置文件必须有可信发布者的签名（和HTTPS或Windows自带的签名机制类似），这也是 Windows Powershell 默认的策略。未经过签名的脚本在 RemoteSigned 策略下运行会产生这样的结果


```shell
PS> .\Start-ActivityTracker.ps1

.\Start-ActivityTracker.ps1 : File .\Start-ActivityTracker.ps1 cannot be loaded.
The file .\Start-ActivityTracker.ps1 is not digitally signed.
The script will not execute on the system.
For more information, see about_Execution_Policies at https://go.microsoft.com/fwlink/?LinkID=135170.
At line:1 char:1
+ .\Start-ActivityTracker.ps1
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [], PSSecurityException
+ FullyQualifiedErrorId : UnauthorizedAccess
```

所以在执行cnpm,yarn,vue...等等工具或插件的指令时,这些脚本或配置文件没有可信发布者的签名,或者不符合Set-ExecutionPolicy脚本安全策略时就无法执行该指令.



#### 1.以管理员身份运行：

Wins+X，然后点击A，即可打开power shell，即管理员身份的命令窗口

#### 2.输入：set-ExecutionPolicy RemoteSigned，然后输入A即可

```
set-ExecutionPolicy RemoteSigned
```

***



## 三.下载源配置

### 1.nvm下载源

安装目录下settings.txt

```ruby
root: F:\nvm
path: F:\nodejs
arch: 64 
proxy: none
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

### 2.node下载源

npm,cnpm,yarn通用配置写法

```ruby
# npm
npm config set registry https://registry.npm.taobao.org --设置淘宝源
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/  --设置node-sass淘宝源
npm config set phantomjs_cdnurl https://npm.taobao.org/mirrors/phantomjs/ -- 设置phantomjs淘宝源
npm config set electron_mirror https://npm.taobao.org/mirrors/electron/   -- 设置electron淘宝源

# cnpm
npm install -g cnpm
cnpm config set registry https://registry.npm.taobao.org --设置淘宝源
cnpm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/  --设置node-sass淘宝源
cnpm config set phantomjs_cdnurl https://npm.taobao.org/mirrors/phantomjs/ -- 设置phantomjs淘宝源
cnpm config set electron_mirror https://npm.taobao.org/mirrors/electron/   -- 设置electron淘宝源

# yarn
npm install -g yarn 
yarn config set registry https://registry.npm.taobao.org --设置淘宝源
yarn config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/  --设置node-sass淘宝源
yarn config set phantomjs_cdnurl https://npm.taobao.org/mirrors/phantomjs/ -- 设置phantomjs淘宝源
yarn config set electron_mirror https://npm.taobao.org/mirrors/electron/   -- 设置electron淘宝源

# 检查结果:
npm config list
npm config get registry
cnpm config list
cnpm config get registry
yarn config list
yarn config get registry
```

***



## 其他相关配置

### 1.修改全局缓存目录

```ruby
npm config set cache F:\nodejs\npm\node_cache\
npm config set prefix F:\nodejs\npm\node_global\
```



### 2.修改PATH

增加PATH如上node所在目录下 F:\nodejs\node_global\ (这个同步修改很有必要, 不然-g全局安装的脚手架如yarn, 命令无法使用)

NODE_PATH 设置到F:\nodejs\ node_global\node_modules 

***



## 四.npx工具

### npx是什么?和npm的关系？

npx是一个内置工具，npm v5.2.0引入的一条命令（npx），一个npm包执行器，旨在提高从npm注册表使用软件包的体验 ，npm使得它非常容易地安装和管理托管在注册表上的依赖项，npx使得使用CLI工具和其他托管在注册表。它大大简化了一些事情。就像npm极大地提升了我们安装和管理包依赖的体验，在npm的基础之上，npx让npm包中的命令行工具和其他可执行文件在使用上变得更加简单。它极大地简化了我们之前使用纯粹的npm时所需要的大量步骤。



### 主要特点：

 1、临时安装可执行依赖包，不用全局安装，不用担心长期的污染。
 2、可以执行依赖包中的命令，安装完成自动运行。
 3、自动加载node_modules中依赖包，不用指定$PATH。
 4、可以指定node版本、命令的版本，解决了不同项目使用不同版本的命令的问题。



### degit 简单项目脚手架工具

degit 是一个简单的利用了github 的项目脚手架工具（当然也支持其他git repo ），使用简单
支持基于cli 以及代码模式的使用

#### 参考使用

- 安装

```ruby
npm install -g degit
```

- 使用

```ruby
npx degit https://github.com/rongfengliang/dremio-user-agent-parse-func#master demoapp
```

