---
title: git 命令行的相关使用
tags: git
categories: 
- 工具
---

git的常用操作：

使用git也很长时间了，一直都是用sourcetree工具辅助。最近有时间就想学习一下直接使用命令行来进行操作，提升一下。

<!-- more -->

```bash
1. 初始化本地仓库 
$ git init 

2. 添加全部已修改的文件，准备提交
$ git add .

3. 将修改后的文件提交到本地仓库
$ git commit -m "提交说明"

4. 连接到远程仓库，创建origin分支
$ git remote add origin "https://github.com/shinyyue/shinyyue.github.io.git"

5. 创建上传流upStream，将本地代码通过upStream推送到origin仓库的master分支上.
$ git push -u origin master
-u ，就是创建 upStream 上传流，如果没有这个上传流就无法将代码推送到 github；同时，这个 upStream 只需要在初次推送代码的时候创建，以后就不用创建了

6. 非首次提交修改文件
$ git add .
$ git commit -m "提交说明"
$ git pull
$ git push

7. 多个远程仓库和多个分支时，pull和push时，需要注明仓库别名和分支名
$ git pull origin master
$ git push origin test
```

> 注意：首次提交之后，再次修改内容提交到git时，执行git pull命令时，若报错refusing to merge unrelated histories，假如我们的源是origin，分支是master，那么我们 需要这样写git pull origin master ----allow-unrelated-histories([参考](https://stackoverflow.com/questions/37937984/git-refusing-to-merge-unrelated-histories))
