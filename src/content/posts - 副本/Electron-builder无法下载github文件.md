---
title: Electron-builder无法下载github文件
published: 2025-02-10 19:19:53
tags: 
  - Electron 
  - HTML 
  - Javascript 
  - CSS
description: 解决electron-builder无法下载GitHub文件的问题
category: Electron
---

个人留档

参考文章：[【Electron】桌面应用开发electron-builder打包报错问题处理-CSDN博客](https://blog.csdn.net/weixin_44490021/article/details/141465519)

# 1.挂全局代理

挂个代理

# 2.本地下载文件并导入

通过cnpm我们下载了electron-builder

运行打包命令后builder会自动进行下载所需要的文件

这时候去GitHub的镜像站之类的把这几个问价下好

下载过程中长时间下不下来就去找日志里的链接

通过GitHub加速或者镜像站或者什么别的方法进行下载之后，对其进行解压

然后分别在`C:\Users\uername\AppData\Local`下的

`C:\Users\username\AppData\Local\electron\Cache`

![](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMRZ6nkDfm4fiVSHww7J-KDpAj4nLIAAsrFMRsVA0hVlA2JwNGGHxQBAAMCAAN5AAM2BA.png)

![](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMSZ6nkMmPio3lUkKBDmdPBhNZ4f_wAAsvFMRsVA0hVA-ZigfHQV4oBAAMCAAN5AAM2BA.png)

在`C:\Users\1\AppData\Local\electron-builder\Cache`中

![](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMTZ6nkboQih89oH1z21SgCnfbFMWgAAszFMRsVA0hVpkRMl2YlqmkBAAMCAAN5AAM2BA.png)

这样的图片不怎么方便，我列个树形图吧



```
C:\USERS\1\APPDATA\LOCAL\ELECTRON
└─Cache
    └─electron-v34.0.2-win32-x64
        ├─locales
        └─resources



C:\USERS\1\APPDATA\LOCAL\ELECTRON-BUILDER
└─Cache
    ├─electron-v34.0.2-win32-x64
    │  ├─locales
    │  └─resources
    ├─nsis
    │  ├─nsis-3.0.4.1
    │  │  ├─Bin
    │  │  ├─Contrib
    │  │  │  ├─Graphics
    │  │  │  │  ├─Checks
    │  │  │  │  ├─Header
    │  │  │  │  ├─Icons
    │  │  │  │  └─Wizard
    │  │  │  ├─Language files
    │  │  │  ├─Modern UI
    │  │  │  ├─Modern UI 2
    │  │  │  │  └─Pages
    │  │  │  ├─UIs
    │  │  │  └─zip2exe
    │  │  ├─Include
    │  │  │  └─Win
    │  │  ├─linux
    │  │  ├─mac
    │  │  ├─Menu
    │  │  │  └─images
    │  │  ├─Plugins
    │  │  │  ├─x86-ansi
    │  │  │  └─x86-unicode
    │  │  └─Stubs
    │  └─nsis-resources-3.4.1
    │      └─plugins
    │          ├─x64-ansi
    │          ├─x64-unicode
    │          ├─x86-ansi
    │          └─x86-unicode
    └─winCodeSign
        └─winCodeSign-2.6.0
            ├─appxAssets
            ├─darwin
            │  ├─10.12
            │  │  └─lib
            │  │      ├─engines
            │  │      └─pkgconfig
            │  └─ci
            ├─linux
            ├─openssl-ia32
            ├─windows-10
            │  ├─ia32
            │  └─x64
            └─windows-6


```

我们可以很清晰的看到，我们需要在electron-builder下创建nsis，winCodeSign，electron-v34.0.2-win32-x64这三个文件夹后才可以再把文件解压进去，如果直接解压文件是识别不到的
