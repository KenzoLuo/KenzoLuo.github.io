---
title: VScode 的安装与配置教程
date: 2020-03-18
tags: vscode
---

Visual Studio Code是一个由微软开发，同时支持Windows、Linux和macOS等操作系统且开放源代码的代码编辑器，它支持测试，并内置了Git 版本控制功能，同时也具有开发环境功能，例如代码补全、代码片段和代码重构等。通过其丰富的插件市场，还是实现很多科研学习中常用的功能，例如markdown、latex的编辑，远程服务器/虚拟机等等。<!--more-->

## 下载与安装

通过[VScode官网](https://code.visualstudio.com)可以下载相应的版本，按说明安装即可。  
唯一需要说明的是，安装完成后有一句提示，“是否将其路径加入环境变量”，选择“是”可以减少不必要的麻烦。  
另外，下文操作都是在windows 10 系统下完成的，其他系统的配置可能有所差异。

## VScode 的配置

下面分别就科研学习中常见的几个需求，进行相关的配置，有的只需要简单的添加插件就行，有的需要进行额外的设置，但是都不复杂，了解相关原理会更且容易理解。

### git

VScode 本身内置了Git，不需要配置。但是Windows 系统是没有git 的（与之对应的，Ubuntu系统是自带git的），需要自己安装。在git 的[官网](https://git-scm.com)上有相关的简介和[下载链接](https://git-scm.com/download/win)，下载后安装说明安装。重新打开vscode 就能自动关联相关程序，不需要额外的操作。

### terminal

VScode 本身不自带终端，通过调用系统其他终端来进行命令行操作。在Linux 系统上有很多可用的终端，比如Ubuntu自带的bash, 颇受欢迎的zsh等，但windows 系统自带的只有cmd 和powershell，都不好用，各种命令和Linux 上也不能一一对应，用起来不方便，更改默认的终端很有必要。鉴于前面装的git里集成了Git bash，这个终端能够满足日常所需，与Linux上的使用习惯也相符，唯一缺陷就是没有包管理，不能再安装新的包。为此，可以选择第三方的终端，cygwin或者git for windows sdk，相当于git bash的加强版，通过pacman进行包管理，补充了git bash的不足，详情见[官方网站](https://gitforwindows.org/) 。

### ssh

参考[另一篇文章](VScode内网穿透的配置教程.md)

### markdown

安装插件Markdown All in One即可满足基本的需求，如果想到导出为PDF、jpeg等格式，可以再安装一个Markdown Preview Enhanced。

### latex

先安装texlive，在官网或者国内的镜像下载。然后在VScode 安装插件texworkshop就能正常使用了，非常简单。
