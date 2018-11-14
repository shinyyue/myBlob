---
title: node搭建web服务器运行build后的静态文件
? date
tags: node
categories:
    - 前端
---

### 背景

在用 vue 搭建项目好之后，想运行 npm run build，在本地看一下打包后的项目效果。可是在浏览器打开 dist 文件中的 index.html 后，页面一片空白。。css/js 资源都找不到。（如下图）

<!-- more -->

<img src="/images/build-html.png" width="400px">

### 资源找不到的原因

要了解资源找不到的原因，首先要知道 vue 的打包机制，dist 文件是怎样生成的。

### 解决办法

1.修改 config 文件夹下的 index.js 文件，将 **assetsPublicPath: "/"** 改为 **assetsPublicPath: "./"**

<img src="/images/build_assets_path.png" width="400px">

> 这里会出现一个问题：修改了之后如果页面还是没有正确显示的话，在注册路由的页面找到 mode 属性，修改 mode 为‘hash’。

2.在本地开启一个 node 服务器。

+ 在 vue 项目中，npm run build 之后，新建 server.js 与 dist 文件同级。

```
var express = require('express')
var app = express()
app.use(express.static('dist'))
app.get('/', (req, res) => {
    res.sendFile(__dirname + '/index.html')
})
app.listen(9002, function() {
    console.log('server is running...')
})
```

+ 在终端执行 node server.js,执行成功后，即可在 localhost:9002/ 下查看项目。
