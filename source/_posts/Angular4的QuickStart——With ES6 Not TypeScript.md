---
title: Angular4的QuickStart——With ES6 Not TypeScript
date: 2017-06-02 18:30:12
tags: Angular,ES6
---



今年3月份，Angular官方发布了新版——Angular4.0.0，新版本的特性已经有很多文章了，在此不一一赘述。

但从Angular2.x以来，JavaScript版本的官方文档就从未完整过，而ES6的QuickStart也从未在日程之内，这对初学者而言多少有点不太友好。虽然网上有ES6+Angular2.x的QuickStart，但是多少有点问题，而且跟Angular4有些不一样，也过时了。今天为了折腾Angular4到处找文档查资料，搞了好久才搞定一个“hello world”，实在有些不爽，为了帮助像我一样的初学者，我觉得把这个ES6的QuickStart写出来也许会有点用。

<!--more-->

> NOTE:本文实现的内容与官方文档中的QuickStart实现的内容没有区别，只是本文是用ES6实现的，而非JavaScript或TypeScript.

废话少说，show the code.

首先是开发环境，先上package.json：

```javascript
//package.json
{
  "name": "angular4-quickstart-es6-webpack",
  "version": "1.0.0",
  "description": "The Angular 4 Quickstart tutorial done in ES6 with webpack and Babel, without using TypeScript.",
  "scripts": {
    "start": "npm run lite",
    "lite": "lite-server",
    "webpack": "webpack --progress",
    "postinstall": "npm run webpack"
  },
  "repository": {
    "type": "git",
    "url": ""
  },
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": ""
  },
  "homepage": "",
  "dependencies": {
    "@angular/common": "^4.0.0",
    "@angular/compiler": "^4.0.0",
    "@angular/core": "^4.0.0",
    "@angular/forms": "^4.0.0",
    "@angular/http": "^4.0.0",
    "@angular/platform-browser": "^4.0.0",
    "@angular/platform-browser-dynamic": "^4.0.0",
    "@angular/router": "^4.0.0",
    "core-js": "^2.4.1",
    "rxjs": "^5.1.0",
    "zone.js": "^0.8.4",
    "es6-promise": "^3.0.2",
    "es6-shim": "^0.35.0",
    "reflect-metadata": "0.1.2"
  },
  "devDependencies": {
    "babel-core": "^6.7.0",
    "babel-loader": "^6.2.4",
    "babel-preset-es2015": "^6.6.0",
    "concurrently": "^2.0.0",
    "lite-server": "^2.1.0",
    "script-loader": "^0.6.1",
    "webpack": "^1.12.14"
  }
}
```

除了Angular官方文档中列出的包和工具之外，还包括了ES6的相关模块及其转码工具Babel。package.json配置好了之后就可以开始配置开发环境了，

目录结构：

### 开发环境：

开发环境的配置需要安装Node([官网](https://nodejs.org/zh-cn/))，然后安装webpack，

```
npm install webpack -g
```

然后在nges6目录下执行：

```
npm install
```

或者执行：

```
cnpm install
```

安装时间可能会有点长，耐心等待，安装完成后，开发所需要的包就会被下载到node_modules文件夹中，接下来是webpack的配置文件：

```
//webpack.config.js
var path = require('path');
module.exports = {
    entry: path.join(__dirname, 'public', 'src','index.js'),
    output: {
        path: path.join(__dirname, 'public', 'dist'),
        filename: 'vendor.js'
    },
    devtool: 'source-map',
    module: {
        loaders: [
            {
                test: /\.js$/,
                loader: "babel-loader",
                query: {
                    presets: ['es2015']
                }
            },
        ]
    }
}
```

配置文件中我们定义了入口文件(public/src/index.js)，然后定义了输出路径和输出文件的命名(public/dist/vendor.js)，webpack将把我们需要的全部依赖打包到一个文件中(vendor.js)，这样我们在HTML中只要用一个script标签引入这个文件就可以了(暂不考虑懒加载的问题)。

不知道上面这部分在说什么的同学，请先以“nodejs”、“npm”、“webpack”等关键词搜索网上的资料。

###  组件

Angular从2.0开始组件化，关于组件，中文文档中介绍说：“组件是一个 Angular 类，用于把数据展示到[视图 (view)](https://angular.cn/docs/ts/latest/glossary.html#view)，并处理几乎所有的视图显示和交互逻辑。组件是 Angular 系统中最重要的基本构造块之一。”组件是一个应用的基础构成，所以，我们首先在app.component.js中定义一个“组件”：

```
//app.component.js
import {Component} from '@angular/core';

class AppComponent {
    static get annotations() {
        return [
            new Component({
                selector: "my-app",
                template: '<h1>My First Angular 2 App</h1>'
            }),
        ];
    }
    
    constructor () {}
}

export {AppComponent};
```

在组件的定义中，需要用装饰器(Decorator)将组件的元数据附加到类上，然后Angular根据这些元数据创建一个组件实例。但是由于ES6目前不支持装饰器语法，因此通过static get annotations()方法完成这一工作。

### 模块：

Angular是以模块的形式来组织代码，每个Angular应用都至少有一个根模块(Root Module)，所以接下来是app.module.js:

```
//app.module.js
import {BrowserModule} from '@angular/platform-browser';
import {NgModule} from '@angular/core';
import {AppComponent} from './app.component';

class AppModule{
	static get annotations() {
		return [
            new NgModule({
				imports: [ BrowserModule ],
                declarations: [ AppComponent ],
		      	bootstrap: [ AppComponent ]
			})
        ];
    }
}

export {AppModule};
```

首先用ES6中的import关键字导入依赖的包(platform-browser/core)以及我们编写的组件(app.component)，然后用class关键字声明我们的根模块(AppModule)。关于impors、declarations、bootstrap的含义，请查阅官方文档中关于根模块的部分[官方文档链接](https://angular.cn/docs/ts/latest/guide/appmodule.html)

### 启动引导：

在main.js中启动(bootstrap)根模块，这个引导文件基本上只写这一次就OK了:

```
//main.js
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';
import {AppModule} from './app.module';

let boot = document.addEventListener('DOMContentLoaded', () => {
    platformBrowserDynamic().bootstrapModule(AppModule);
});

module.exports = boot;
```

### index.js

index.js用于告诉webpack需要打包的依赖都有哪些。

```
//index.js
require('!!script!../../node_modules/es6-shim/es6-shim.min.js');
require('!!script!../../node_modules/core-js/client/shim.min.js');
require('!!script!../../node_modules/zone.js/dist/zone.js');
require('!!script!../../node_modules/rxjs/bundles/Rx.min.js');
require('!!script!../../node_modules/@angular/core/bundles/core.umd.js');
require('!!script!../../node_modules/@angular/common/bundles/common.umd.js');
require('!!script!../../node_modules/@angular/compiler/bundles/compiler.umd.js');
require('!!script!../../node_modules/@angular/platform-browser/bundles/platform-browser.umd.js');
require('!!script!../../node_modules/@angular/platform-browser-dynamic/bundles/platform-browser-dynamic.umd.js');


require('./app/main');
```

### index.html

最后是index.html文件：

```
<!DOCTYPE html>
<html>
<head>
    <title>Angular 4 QuickStart</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>

<body>
    <my-app>Loading...</my-app>

    <script type="text/javascript" src="public/dist/vendor.js"></script>
</body>
</html>
```
代码写好了，接下来是两个命令：

```
webpack --progress
npm start
```

这两个命令会将所有需要的js模块打包到vendor.js中，并启动一个本地服务器，调用你的浏览器，你会看到你的应用跑起来了：

<img src="../../../resource/my-first-angular-app.jpg"/>

由于本人也是入门级选手，很多东西也没搞清楚，所以文章写得很简单。但是其中的诸多概念都有不少文章有所论述，可以在网上查到，我也不可能说得更明白，所以就不拾人牙慧了，更深入的内容留待以后探讨。本文主要是提供一个基于webpack+ES6的QuickStart，如有问题，欢迎留言探讨。