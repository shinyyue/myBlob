---
title: nginx操作
date:
tags: nginx
categories:
    - nginx
---

```bash
cd /                            #进入到根目录
cd /usr/local/nginx/sbin        #进入nginx的安装路径
```

<!-- more -->

```bash
#进入 nginx 安装目录，查询 nginx 进程:
cd usr/local/nginx/sbin
ps -ef | grep nginx
```

```bash
#修改 nginx 配置：
vim usr/local/nginx/conf/nginx.conf
```

```bash
#修改完配置文件后需要停止服务，重启后立即更新：
cd usr/local/nginx/sbin
./nginx -t              #检测是否配置有问题
./nginx -s stop         #nginx停止服务
./nginx                 #启动
```
