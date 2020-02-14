---
title: webpack入门
date: 2018-12-23 15:31:16
tags:
---

# 概念

## *node.js、npm*和*webpack*

*node.js*是一个基于 Chrome V8 引擎的 JavaScript 运行环境，是运行在服务器端的JavaScript，可以摆脱浏览器宿主环境，解析和执行js代码，直接进行底层系统调用，并且内置提供了HTTP服务器相关的大量API。 *npm*随node.js一起安装，是一个包管理工具，用来解决node.js代码部署问题。 *webpack*是前端工程化打包工具，可以处理功能丰富的大型网站所拥有的复杂的js代码、css代码等，解析它们的依赖关系，转换和打包成模块使用，实现自动化管理前端代码。

# 功能

从功能上来说，node.js可以开启一个Web服务，给浏览器提供数据去展示，并且接收浏览器提交过来的用户产生的数据，存储到数据库中，方便后面使用。 我们在代码中使用node.js打包好的模块（模块指具有相同功能的代码的集合），然后需要通过npm工具为我们的工程导入模块以便代码在本地调用，npm工具被包装成一个类似命令行的模式提供给开发者使用。除了调用node.js已有的模块，我们还可以在npm上发布自己的模块和调用其他人所发布的模块。 webpack则是运行在npm上的一个任务执行者，可以看作npm为开发者提供的一个功能，和webpack实现类似功能的主流构建工具还有grunt，gulp，browerity等，其中webpack非常适用于管理前端开发代码，它将一切文件模块化，可以方便地帮助我们简化前端复杂繁多的js文件、css文件的调用，使我们的web应用工程化。 有用的资料：[webpack官方网站](https://webpack.js.org)

# *webpack*使用

前提：本地环境支持node.js

## 全局安装webpack

`npm install webpack -g`

全局安装webpack 

webpack4，命令行相关的内容都移到 webpack-cli，所以还需要安装 webpack-cli 

`npm install webpack-cli -g`

# 创建项目

`mkdir app` 

项目文件夹

在项目路径下 

`npm init –yes`

创建package.json文件

package.json 是包描述文件，存储着包的信息（包名、版本、项目的依赖项），最好每个项目都要有个 package.json 文件，就像产品说明书。此文件可自己普通文本改名的方式创建，但一般是 npm 命令创建。运行npm init命令会以向导的方式填写包的信息，不想填的话可回车略过。–yes是快速创建，可以跳过信息的填写直接创建，之后想改可以直接到package.json文件中修改。

## 局部安装webpack

`npm install webpack –save-dev`

为什么全局安装了还要局部安装：[npm全局安装和局部文件安装区别](https://www.cnblogs.com/linziwei/p/7786895.html) 关于–save和-dev可参考：[npm –save和–save-dev 傻傻分不清](https://www.limitcode.com/detail/59a15b1a69e95702e0780249.html) 接下来分别示例打包js文件和非js文件(css)两种使用场景来说明webpack使用的基本步骤

## JS文件

在app目录下创建test.js文件并保存

`document.write(“it works!”);`

打包为bundle.js 

`webpack test.js bundle.js`

注意，如果是高版本的npm，比如我的是5.6.0，需要加-o 

`webpack test.js -o bundle.js`

## CSS文件

创建style.css文件 

```
body { background: pink; }
```

修改test.js文件 

```javascript
require(“!style-loader!css-loader!./style.css”);//引用style.css文件 document.write(require(“it works!”);
```

> Webpack 本身只能处理 JavaScript 模块，如果要处理其他类型的文件，就需要使用 loader 进行转换。 所以如果我们需要在应用中添加 css 文件，就需要使用到 css-loader 和 style-loader，他们做两件不同的事情，css-loader 会遍历 CSS 文件，然后找到 url() 表达式然后处理他们，style-loader 会把原来的 CSS 代码插入页面中的一个 style 标签中。

为了使用style-loader和css-loader，同样我们也需要全局安装style-loader和css-loader 

`npm install style-loader css-loader -g`

再局部安装 

`npm install style-loader css-loader –save-dev`

打包

`webpack test.js -o bundle.js`

浏览器打开index.html查看效果

## 配置 webpack.config.js

如果我们需要打包多个文件，那么在命令行下一个个输入文件名会比较麻烦，所以我们一般通过配置webpack.config.js文件，然后就可以直接使用webpack命令来打包所有文件。 创建webpack.config.js文件 

```javascript
module.exports = { 
	//指定入口文件 
    entry: { entry: ‘./test.js’ }, 
    //指定出口文件 
    output: { path: __dirname, filename: ‘test.js’ }, 
    //模块,指定加载器,可配置各种加载器,test里指定加载文件的格式(.css)，loader里指定加载器名称 
    module:{ rules:[{test:/\.css\$/}, 
    loader:”style-loader!css-loader”] 
           }
```

注意，webpack2之后将loaders改为了rules，如果还是写成

```javascript
loaders:[{test:/\.css\$/}, loader:”style-loader!css-loader”]
```

将报错

在指定出口文件中，`path:__dirname`path指生成的出口文件的统一路径，__dirname表示当前目录。

如果需要生成的文件在别的目录下，可以在文件开始引入模块path

`const path=require(‘path’);`

然后就可以使用path.join()方法，这种方法将多个参数字符串合并成一个路径字符串

`path: path.join(__dirname,’dist’)`

出口文件会在/app/dist文件夹下，如果没有dist文件夹会自动创建

或者path.resolve()方法，这种方法以程序为根目录，作为起点，根据参数解析出一个绝对路径

`path: path.resolve(__dirname,’./dist’)`

关于它们的区别：[path.join()和path.resolve()](https://blog.csdn.net/qq_33745501/article/details/80270708)