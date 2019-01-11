---
title: 创建一个weex demo在ios模拟器上执行
date:
tags: weex
categories:
    - 前端
---

被 weex 官网坑死了。。一步步的按照官网的集成做，最后发现不需要这么复杂了。官网没更新。。哭死。。终于在即将放弃时，在 jira 上找到了正解。还是期望 weex 官方维护人员能及时更新文档。以下是创建一个 weex demo,并运行在 iOS 模拟器的全部过程。

### 一、搭建开发环境

-   安装 node.
-   安装脚手架 weex-toolkit. npm install -g weex-toolkit
-   查看 weex 版本。 weex -v

<!-- more -->

### 二、利用 weex-tollkit 脚手架初始化一个项目

-   weex create my-project
-   cd my-project
-   npm install
-   npm run dev & npm run serve. 开启 watch 模式和静态服务器

启动成功后，浏览器访问 http://localhost:8080/index.html

### 三、本地模拟机查看项目

iOS 端

(一) 工具配置

    首先需要下载3个工具：xcode、xcode上下载模拟器、CocoaPods.

    模拟器：xcode —> performance —> comonnets —> 选择一个模拟器下载
    CocoaPods：（用途）

    CocoaPods下载方法：

-   下载 ruby. Mac 电脑自带 ruby 源，不需要下载，更新就好。
    -   sudo gem update —system
-   替换为国内 ruby 源
    -   gem sources --remove https://rubygems.org/ 先移除
    -   gem source -a https://gems.ruby-china.com/再替换
    -   gem source -l 确认源只有淘宝的源
-   下载 CocoaPods
    -   sudo gem install cocoapods
        -   报错：While executing gem ... (Gem::FilePermissionError) You don't have write permissions for the /usr/bin directory，是因为无写入到/usr/bin directory 权限。
        -   解决：sudo gem install cocoapods -n /usr/local/bin
    -   进入 weex 目录下 playground/ios 下，执行 pod install

(二)添加 ios 平台

weex platform add ios

(三)添加依赖

-   修改 podfile 文件

```bash
    source 'git@github.com/CocoaPods/Specs.git'
    platform :ios, '8.0'
    #inhibit_all_warnings!

    def common
    pod 'WeexSDK'
    pod 'WeexPluginLoader'
    pod 'SDWebImage', '3.7.5'
    pod 'SocketRocket', '0.4.2'
    pod 'WXDevtool', '0.15.3', :configurations => ['Debug']
    end

    target 'WeexDemo' do
    common
    end

    target 'WeexUITestDemo' do
    common
    end

    # fixme:
    post_install do |installer|
        installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|
            if config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'].to_f < 8.0
                config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '8.0'
                end
            end
        end
    end
```

-   进入 paltforms/ios, 执行 pod install

(四)在 iOS 模拟器上运行

执行 weex run iOS, build 成功之后将会打开模拟器，并运行项目。
