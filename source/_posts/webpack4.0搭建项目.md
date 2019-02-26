---
title: webpack4.0搭建项目
date:
tags: webpack
categories:
    - 前端
---

### webpack 搭建项目

1.首先我们使用 webpack 搭建一个项目

    mkdir webpack-test

2.进入文件下。

    cd webpack-test

3.初始化项目

    // 执行成功后，文件夹出现新的文件 package.json
    npm init

<!-- more -->

4.在测试环境安装 webpack/webpack-cli

    npm install —save-dev webpack webpack-cli

5.根目录下新建 webpack.config.js 文件，以便编写 webpack 的基本配置。

```bash
const webpack = require("webpack")
const path = require("path")

exports.default = {
    entry: './src/index.js',
    output: {
        path: path.resolve(\_\_dirname, 'dist'),
        filename: 'bundle.js'
    }
}
```

6.修改 package.json，添加运行命令，以便控制台输入 npm start 编译项目。

```bash
"scripts": {
    "start": “webpack"
}
```

7.执行 npm start 命令。即可看到 dist 文件夹下新增 bundle.js 文件。

### babel 的安装、配置

随着 es6 的普及，越来越多的项目开始使用 es6,但是浏览器并不能编译 es6 的语法，所以我们需要一种转换器，来将项目中的 es6 代码转换为 es5 代码。babel 便是做这个事情的。下面是 babel 的配置。

8.下载 babel 相关插件

```bash
npm install -D babel-loader @babel/core @babel/preset-env webpack webpack-cli
```

9.修改 webapck.config.js 的配置。

```bash
module: {
   rules: [{
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
            loader: 'babel-loader'
        }
   }]
}
```

10.执行 npm start，即可在 bundle.js 文件中找到转换后的的 es5 代码。
