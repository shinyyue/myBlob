---
title: '搭建hexo博客'
categories: 
- 工具
tags: hexo
comments: true
---

-----------

## 安装hexo

* 需要安装Node.js和Git应用

* 全局安装hexo

``` bash 
$ npm install -g hexo-cli
```

<!-- more -->

## 创建hexo

``` bash 
    $ hexo init <folder>

    $ cd <folder>

    $ npm install
```

## 修改主题

1.选择自己喜欢的[主题](https://hexo.io/themes/).

2.下载选中主题. 例如：

``` bash
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

3.并在_config.yml中修改配置.  例如: theme: yilia

## 新建文章

``` bash
$ hexo new [layout] <article_name>   layout默认为post

$ hexo new post test     则在_posts文件中新建标题为test的文章
```

## 新建文章分类

1.在主题的_config.yml配置文件中配置，文章分类menu中即可显示。

``` bash
menu:
    主页: /
    前端: /categories/前端
    随笔: /tags/随笔
```

2.新建文章后，可根据文章内容设置合适的分类。

``` bash
---
title: 前端文章
categories: 
- 前端
---
```

## 运行项目

```bash 
$ hexo g      生成静态文件

$ hexo server  启动服务器
```

## 部署项目到GitHub

1.在github中建立一个库，名称为yourAccountName.github.io,例如’shinyyue.github.io‘;

2.在_config.yml中添加配置
``` bash
deploy:
  type: git
  repo: https://github.com/shinyyue/shinyyue.github.io.git
  branch: master
  message: hexo deploy
```

3.安装 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)

```bash
$ npm install hexo-deployer-git --save     
```

4.部署

```bash
$ hexo d   
```

5.打开[地址](https://shinyyue.github.io/)

## 添加评论功能

1.gitment是一款基于Github Issues的评论系统。无需后端代码，前端即可引入使用。

2.添加步骤：

```bash
在github上创建oAuth Apps获取client_id和client_secret。

在主题配置文件下开放gitment评论设置。
```

![创建oAuthApp1](/images/create-oAuthApp.jpeg)

![创建oAuthApp2](/images/create-oAuth-info.jpeg)

![开放gitment评论设置](/images/gitment.jpeg)

## 添加访问量统计

使用不蒜子统计。

引入js文件：
```bash
<script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

在需要添加统计功能的位置添加下面代码：
```bash
<span id="busuanzi_container_site_pv">
    本站总访问量
    <span id="busuanzi_value_site_pv"></span>次 本站访客数
    <span id="busuanzi_value_site_uv"></span>人次 本文总阅读量
    <span id="busuanzi_value_page_pv"></span>次
</span>
```


## 遇到的问题

1.如何使文章在首页只显示一部分？

``` bash
在md文件中适当的位置添加 <!-- more -->进行截断，即可在首页仅显示该阶段标识以上的部分；

此时，more>> 和 展开全文按钮同时存在，需要去掉一个；

主题目录下的_config.yml文件中，修改excerpt_link值为空，即可去掉more>>。

```

2.添加评论时出现Error: Not Found

可能的原因是：repo库写错，这里repo指的是github中的你用来记录issue的库。