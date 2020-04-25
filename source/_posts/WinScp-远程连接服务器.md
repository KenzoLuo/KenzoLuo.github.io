---
title: WinScp 远程连接服务器
date: 2020-04-01 11:22:10
author: 罗健
tags: server
---

[WinSCP](https://winscp.net/eng/index.php)是一款开源的SFTP客户端，运行于Windows系统下，遵照GPL发布。WinSCP除了SFTP，还支持SSH、SCP。在日常的科研工作中，经常需要将计算结果从服务器端下载下来进行分析，或者将计算输入文件上传到服务器。从名字就能看出来，Windows+SCP=WinSCP，在Windows系统下通过SCP来完成这些文件上传/下载操作。当然，它最主要的优势还是图形界面，对于初学者比较友好，学习成本低。这里记录一下，WinSCP的配置过程，实现在校园网/校外网环境访问实验室服务器。<!-- more -->

## WinSCP的安装

从官网下载最新版安装包，按照提示安装即可。

## 配置站点

打开WinSCP，会弹出一个站点选择和设置的窗口，如下图所示，需要填入相应的信息，或者直接导入配置文件。
![winscp-site](/image/winscp-site.png)

### 校内使用

由于服务器是在校园网下的，在校内使用时，可以直接通过ip地址来访问，在新站点页面以此填入相应信息，可以选择保存到常用列表，并记住密码，这样就免去了每次输入密码的繁琐。

### 校外使用

在校外使用时，需要通过连接了外网的“跳板机”来实现，和在[VScode内网穿透的配置](https://kenzoluo.github.io/2020/03/13/VScode%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F%E7%9A%84%E9%85%8D%E7%BD%AE%E6%95%99%E7%A8%8B/)中介绍的原理一样。在WinSCP中需要在新建站点时，选择高级选项来进行修改。
![advanced](/image/advanced.png)
然后在高级选项中选择SSH Tunnel，勾选相应选项，填入跳板机的信息，如下图所示，应用后回到新建站点的页面，保存即可。
![ssh-tunnel](/image/ssh-tunnel.png)

## 常见问题

暂无
