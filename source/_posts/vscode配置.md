---
title: vscode开发vue的基本配置
? date
tags: vscode
categories:
  - 前端
---

### 应用下载

1.!(vscode官网下载)[https://code.visualstudio.com/]

2.下载中文语言包扩展。在扩展商店中搜索[Chinese (Simplified) Language Pack for Visual Studio Code]

### 主题界面配置

1.主题配置：整体颜色温和，代码高亮色明显而不刺眼。

<!-- more -->

```
"workbench.colorTheme": "One Dark Pro"
```

2.文件图标。扩展商店搜索vscode-icons。但是我觉着太丑了。还是没有好看。

    使用方法：商店下载完后手动点击重新加载即可在code->首选项->文件图标主题中找到。需单独为每个项目配置。

3.字体配置。

字体配置：
```
"editor.fontFamily": "Menlo, Monaco, 'Courier New', monospace",
```

### 开发常用插件

1.vetur:

    Vetur支持.vue文件的语法高亮显示，除了支持template模板以外，还支持大多数主流的前端开发脚本和插件，比如Sass和TypeScript  

    vetur格式化html配置：
    ```
     "vetur.format.defaultFormatter.html": "js-beautify-html",
    ```

    emmit配置：
    ```
    "emmet.syntaxProfiles": {
        "vue-html": "html",
        "vue": "html"
    }
    ```
  
2.VSCode中使用vetur插件格式化vue文件时，js代码会被添加上分号且单引号会转变为双引号,空格数也变成了2个。

    下载prettier插件。分号及单引号及空格个数的配置：

    ```
        "prettier.semi": false,
        "prettier.singleQuote": true
        "prettier.tabWidth": 4
    ```

3.eslint插件

4.自动闭合html插件Auto Close Tag

5.修改html标签时，自动修改匹配的标签插件Auto Rename Tag

6.Git Blame在状态栏显示当前行的Git信息

7.Git History(git log) 查看git log

8.GitLens 显示文件的作者以及当前行的commit信息

9.TODO Highlight todo高亮

    todo配置：
    ```
    "todohighlight.keywords": ["todo:", "fixme:"]
    ```

### 常用快捷键

1.打开新窗口 command + shift

2.关闭窗口 command + shift + w

3.打开命令面板 command + shift + p

4.选中一行 command + shift + 左右箭头

5.选中当前内容 command + d

6.打开建议splotlight command + space

7.截屏：command + shift + 4






