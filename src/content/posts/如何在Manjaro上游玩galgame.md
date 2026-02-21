---
title: 如何在Manjaro上游玩galgame
tags:
  - Linux
  - Manjaro
  - Galgame
category: [Galgame,Linux]
comments: true
image: 'https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMPZ531Nzx4VJLZPS1qKQdKKscBxXgAAjzBMRvE5fFULPspNWj8vU8BAAMCAAN5AAM2BA.png'
top_img: 'https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMPZ531Nzx4VJLZPS1qKQdKKscBxXgAAjzBMRvE5fFULPspNWj8vU8BAAMCAAN5AAM2BA.png'
abbrlink: 1aad9c90
published: 2025-01-31 13:21:29
---

# 引言

{% note warning %}

此文章并不一定百分百有效，具体请看个人

本文对于一些汉化过的galgame并不百分百有效

{% endnote %}

有关Arch Linux游玩galgame的文章倒是不少，但是Manjaro倒是没几个

于是乎今天就来写写有关Manjaro的

起初我也想安装Arch来着，但是安装的时候挂载挂错了，把EFI安到别的分区了遂放弃（其实就是懒得再去装一遍了，直接选择Manjaro的GUI安装）

# Wine兼容层与Proton

首推的最方便的游玩方式

通过wine和proton不仅可以玩galgame，也可以玩一些别的游戏

由于Manjaro的Wiki东西很少，于是乎就写了这篇文章

首先，介绍一下wine兼容层

> [Wine 是一款免费和开源的**兼容层**，使得在运行 Linux、FreeBSD 或其他一些操作系统的计算机上运行一些 Windows 应用程序成为可能。它也是 Valve 用来让 Steam Deck 用户在 Linux 驱动的手持设备上玩 Windows 游戏的 Proton 软件的基础。Wine（Wine Is Not an Emulator）能够在多种兼容 POSIX 接口的操作系统上运行 Windows 应用

看到这里你应该明白wine和proton的关系了，proton就是v社基于wine开发的工具（你的steam deck能玩Windows游戏就是proton的功劳）

&nbsp;

## Wine

在安装wine之前，你得先至少准备好Windows下的所有字体文件，不然会乱码

Windows字体文件`C:\Windows\Fonts`,打包后存好

然后切到你的Manjaro

在Manjaro下安装wine安装 Winemultilib 存储库，利用vim进入 `/etc/pacman.conf` 将`[multilib]`部分取消注释即可

输入

```bash
sudo pacman -S wine wine-mono wine-gecko
#稳定版
sudo pacman -S wine-staging wine-mono wine-gecko
#测试版
```

这样的话，一个基本的wine就安装好了
当然你也可以安装更多的依赖，详见[Arch Wiki](https://wiki.archlinuxcn.org/wiki/Wine#%E5%8F%82%E8%A7%81)
请在参阅arch wiki的时候记住你用的是Manjaro而不是Arch，提问请去Manjaro的社区

```bash
wine --version
```

通过此来查看自己的版本

输入后

```bash
wine regedit
```

这时候会自动跳转出来一个界面，大概如下
![win regedit](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMJZ5zbDP2IBFoXSGQjPxtou6s1wAQAAsLCMRvQjelUR64o8YFsDw0BAAMCAAN3AAM2BA.png)
你会发现，诶，文字怎么变成方格了
这其实是缺失字体导致的，解决方法也很简单，将你准备好的字体移动到`/home/${username}/.wine/drive_c/windows/Fonts`（如果找不到`.wine`请打开显示隐藏文件）
创建`zh.reg`
```bash
 REGEDIT4

[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes]

“Arial”=”simsun”

“Arial CE,238”=”simsun”

“Arial CYR,204”=”simsun”

“Arial Greek,161”=”simsun”

“Arial TUR,162”=”simsun”

“Courier New”=”simsun”

“Courier New CE,238”=”simsun”

“Courier New CYR,204”=”simsun”

“Courier New Greek,161”=”simsun”

“Courier New TUR,162”=”simsun”

“FixedSys”=”simsun”

“Helv”=”simsun”

“Helvetica”=”simsun”

“MS Sans Serif”=”simsun”

“MS Shell Dlg”=”simsun”

“MS Shell Dlg 2”=”simsun”

“System”=”simsun”

“Tahoma”=”simsun”

“Times”=”simsun”

“Times New Roman CE,238”=”simsun”

“Times New Roman CYR,204”=”simsun”

“Times New Roman Greek,161”=”simsun”

“Times New Roman TUR,162”=”simsun”

“Tms Rmn”=”simsun”
```
导入之后重启wine即可

```bash
winecfg
```
通过此可以进行更多的设置
自此可以使用wine了

## wine进阶
虽说能运行一些小游戏是不错，但是对于galgame还是不够的
WINEARCH这个变量决定了你模拟的Windows是32位或是64位的x86。对应的值为win32及win64，如果你的系统是64位的它就默认是win64
在64位的情况下运行32位的软件会出错
```bash
WINEARCH=win32 WINEPREFIX=/home/你的username/.wine_32 winecfg
#指定路径创建32位的文件夹
```
我个人不建议直接删掉.wine文件夹然后直接让他重新配置，而是创建一个wine_32文件夹来指定这个这个文件夹里运行32位应用
`WINEPREFIX`这个路径是可变的，你可以随便取名

但是运行32位应用时需指定
```bash
WINEPREFIX=~/.wine_32 wine program.exe
```
否则仍然按照默认的wine64运行

&nbsp;

在windows上玩游戏经常会遇到dll缺失带来的报错，那么wine上就更多了

安装`winetricks`
```bash
yay -S winetricks
```
（注：yay是AUR的软件仓，具体请百度搜索或者参考Arch Wiki）
然后在终端运行winetricks
```bash
winetricks
```

![如图](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMKZ5zjMIkhYmOdG_dydkq2TkoUA8sAAuHCMRvQjelUFNI6dkUSgiQBAAMCAAN5AAM2BA.png)

会出现如图GUI，意思是让你选一个wine的文件夹，默认就好
这时候他会问你要不要发送数据这样利于winetricks的开发，看个人，我点的yes
![](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMLZ5zkBKi3REWzqdb3lYM8ep-2V3cAAuLCMRvQjelU6rM6oRx8XbgBAAMCAAN5AAM2BA.png)
点击安装dll就好了
他会让你选择安装的dll,值得注意的是，winetricks安装的dll有点是仅支持32位的
所以选择安装dll时得注意

其实光下载一个wine在下载好依赖就可以应对绝大部分的galgame了


如果嫌麻烦可以使用playonlinux来管理wine的使用和磁盘数


## Proton
proton的易用性比wine好很多
```bash
sudo pacman -S steam
```
通过此命令下载steam
登陆账号之后在设置里启用
![设置界面](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMMZ53si7yA7wyKoQQB5ZHcJ-Bi6VAAAi7BMRvE5fFUuUJFJ9342iYBAAMCAAN5AAM2BA.png)
通过兼容性来启用steam的proton
然后把你想要运行的exe文件添加到steam
添加到steam之后点击齿轮图标
![](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMNZ53tAcViNFBeQU8c_iZBBqiZ5oAAAjDBMRvE5fFUxxGNuvecau0BAAMCAANtAAM2BA.png)
找到`属性-->兼容性-->强制使用特定steam play兼容性工具`
![](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAAMOZ53tctnDwjNh3RjBH9TK900cw1UAAjLBMRvE5fFUD5R8ipFAicgBAAMCAAN4AAM2BA.png)

选择你要使用的proton版本，之后点击启动，steam就会下载proton了
***


# 虚拟机
虚拟机说实话我并不推荐，如果想使用虚拟机不如买个盘装双系统
一个是虚拟机卡，一个是虚拟机不方便
虚拟机软件例如vmware,kmv，VirtualBox等知名软件都可以使用
兼容性肯定比wine强，但是运行效率就不尽人意了
所以双系统>>wine/proton>>虚拟机

