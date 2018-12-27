---
title: aws搭建shadowsocks
date:
tags: shadowsocks
categories:
    - 工具
---

1.创建实例。

按步骤创建 linux 实例。添加安全组的入站规则。

2.ssh 方式连接服务器。

-   chmod 400 shiny-pem.pem

-   ssh -i "shiny-pem.pem" ec2-user@ec2-52-195-7-182.ap-northeast-1.compute.amazonaws.com

<!-- more -->

-   进入根目录，查看当前用户的用户名，并查看根目录下所有文件的权限

    ```bash
    1. cd /
    2. whoami
    3. ls -l
    ```

-   查看后发现，文件都是 root 用户才有的权限。

3.创建 root 用户，并设置密码。

```bash
sudo password root
输入密码
再次确认密码
```

4.进入 root 用户,修改配置文件，以允许密码登录服务器。

```bash
#切换root用户
su root
# 编辑文件，修改PasswordAuthentication/PermitRootLogin为true,使系统允许密码登录。
vim /etc/ssh/sshd_config
# 重启服务。
/sbin/service sshd restart
```

5.在 root 用户下,下载 shadowsocks,并配置 shadowsocks.json 文件。

```bash
#安装shadowsocks
yum install python-setuptools && easy_install pip
pip install shadowsocks
# 配置shadowsocks.json文件（需要新建一个shadowsocks.json文件）
vim /etc/shadowsocks.json (要确保8388端口已开放)
{
    "server" : “0.0.0.0”,
    "server_port" : "8388",
    "local_address": "127.0.0.1",
    "local_port" : 1080,
    "password" : "yourpwd",
    "timeout" : 600,
    "method" : "aes-256-cfb"
}
#启动ss服务器
ssserver -c /etc/shadowsocks.json -d start
```

6.客户端下载 shadowsocks-ng，配置上各项即可。
