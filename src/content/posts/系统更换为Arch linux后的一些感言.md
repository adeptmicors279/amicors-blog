---
title: Arch Linux 折腾记录
published: 2026-02-22 12:04:53
tags:
  - Arch
  - Linux
description: 折腾Arch Linux的一些记录
category: Linux
draft: false
---
# 前言

最近把系统从Manjaro换成了Arch,虽说Manjaro也是Arch系的发行版，但是整体体验上还是劣于Arch（个人感觉

Arch最麻烦的还得是纯cli安装，虽然有自动安装的archinstall但是给我的感觉还不如手动安装

我个人配置的桌面环境是hyprland+waybar
~~waybar配置的好丑不要在意~~

![](https://img.amicors.cc/file/AgACAgUAAyEGAASEePlKAANiaasDK9fvp89N1xDwCvJBWPg5h8UAAswNaxsAATBYVTKJVIAMuPHUAQADAgADdwADOgQ.png)

其实Arch最吸引我的是它的滚动更新和毛胚房一样的系统，自定义性很强，可以按照喜好定制


# 桌面环境
在我还在使用windows的时候就已经在网上看到过hyprland桌面的视频了

我看完视频后（转头看看win的桌面）瞬间觉得桌面好难看，自此就对平铺桌面充满了向往

## GNOME
虽然喜欢平铺桌面，但是我第一个使用的桌面却是GNOME，系统是Ubuntu，安装在虚拟机

其实硬要说的话当时也仅仅是为了尝鲜所以使用了Ubuntu，在尝鲜过后就删除了

## KDE
KDE作为我第一个使用时间（相对）较长的桌面，当时是用的Manjaro，Arch的衍生版，我对于他的评价是，丝滑但是很别扭

我知道凭我的水准完全不足以给一个大佬们维护的桌面下如此的评价，但是用起来却是很别扭(((

动画方面确实没的说，吊打win，但是操作逻辑上（可能是我用习惯win了）有点难受

而且堆叠桌面我感觉可美化的地方不是很多

我都用linux了结果还是平铺那我为什么不用回windows呢    ~~结果真用回windows了~~

## hyprland
在前几天的更新中发现hyprland的super+j失效了，去网上查找解决方案发现是官方更新导致配置文件变动，由此了解到了hyprland前几次还进行过破坏性极强的更新

但是尽管不稳定我还是爱用（）

第一次上手平铺桌面给我的感觉就是，桌面还要有这么多快捷键要记，好麻烦

结果习惯之后就回不去堆叠桌面了

相较于堆叠桌面，平铺给我的直观感受不仅仅是有多美观，而是窗口带来的信息量

以及不用去一个个拖窗口自动排好的便捷感（

~~抛掉鼠标之后感觉自己的右手意外地轻盈?~~

而且最近hyprland更新了scrolling布局，其实还是挺方便的


# 包管理器及AUR

## pacman
在使用Arch的pacman之前曾使用过Ubuntu的apt

~~我记得之前好像有首歌很火就叫apt来着（）总是会莫名联想~~

pacman真乃懒人神器也

简单易用，加几个参数就可以轻松做到搜索软件包，不像apt还要search

## AUR
早有听闻AUR的大名

如今上手确实很好用，各种软件都有（貌似edge好像在hyprland下会闪烁）

# 滚动更新与快照
网上都说Arch更新容易滚挂，其实用起来好像也没那么邪乎

`pacman -Syyu`一句话就能更新系统确实方便

不过为了防止滚挂于是用了timeshift快照

貌似听说timeshift有些bug不怎么稳定，最近打算换成snapper

# 社区wiki
Arch的wiki可以说是我看过的很优秀的wiki了，很多东西都很详细，易于上手

***
这些大概就是换成Arch的感想，Arch我大概会一直用下去，平铺桌面太香了