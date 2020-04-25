---
title: VScode 内网穿透的配置教程
date: 2020-03-13
tags: 
- vscode
- server
---

实验室的计算服务器都是处在学校内网的,在校内可以通过 ssh 就能够远程访问.之前常用的工具是WinScp+putty,来实现文件的上传下载以及命令行的交互,如今 VScode 能够更方便和统一的完成这些工作,通过 Remote Development 插件连接上远程服务器，然后打开服务器上的文件夹作为 workspace,无需将服务器上的各种输入文件和源码在本地和服务器之间来回拷贝.

但是,离开了学校,在外网就无法连接到实验室的服务器了.一个办法是在服务器登录网络通账号,但是这样以来,由于实验室所有服务器连在一台路由器上,所有的机器都会暴露在外网上,实验室的数据安全存在风险.另一个办法就是通过在实验室的一台连接外网的机器(自己登录了校园网的小机器，博闻哥提供的ablabor，老师联网了的机器等),当作"跳板机"来连接内网内的其他服务器.具体实现记录如下.<!-- more -->

## 1.准备工作

整个连接的逻辑就是: local -> jump -> server, 操作系统分别是:  
>local(本地电脑): windows 10（或者Ubuntu）  
>jump(跳板机): Ubuntu  
>server(服务器): Ubuntu

### 1.1 配置免密登录

为了方便日常使用,可以通过公钥私钥的方式实现 local 对跳板机 jump 和服务器 server 的免密登录.步骤如下（如果不嫌每次输密码麻烦，或者不是在自己的电脑上登录，可以跳过这一步）

1. 进入 PowerShell（或者bash）  
2. 输入 `ssh-keygen -t rsa -b 4096`, 从而生成了一对公钥和私钥,放在个人用户目录根目录下的隐藏文件夹 ssh 下,即 C:\用户(user)\luojian\ .ssh\ ,文件夹中包括
    >id_rsa：私钥，本地机器持有,无需操作  
    >id_rsa.pub：公钥，后面需要上传到希望免密登录的机器上去  
3. 上传公钥到跳板机和服务器,把 id_rsa.pub 里的内容复制到跳板机和服务器的 ~/.ssh/authorized_keys 文件中(如果没有请新建相应文件夹)。

4. 测试是否能通过 ssh 实现直接连接到跳板机和服务器

### 1.2 安装最新版Openssh

Windows 平台的 Openssh 套件中并不包含原生的适用于 ProxyCommand 的工具, 为了避免后面出现问题,这里先安装最新的适用的 Openssh 套件,步骤如下（如果自己电脑也是Ubuntu，就可以省略这一步）

1. 以管理员身份运行 PowerShell. 使用快捷键 Windows + X,接着按 A 或点选 Windows PoweShell（管理员）  
2. 输入`Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0`, 在线安装Openssh,若安装成功则屏幕会打印

    ```bash
    Path          :
    Online        : True
    RestartNeeded : False
    ```

## 2. VScode 的配置

1. 安装VScode，不再赘述。
2. 安装 Remote - SSH 插件。（插件 Remote Development 三件套中的另两个插件可以不用装，这里用不到）
3. 配置本地~/.ssh/config文件  
   在vscode中我们点击新生成的连接按钮，如果没有config文件的话点击configure，根据提示新建config文件，在其中填入如下内容

```SSH config
# 跳板机jump的配置
Host jump                       #跳板机的标签
  HostName 210.76.XXX.XXX       #跳板机的ip地址
  User user_jump                #在跳板机上的用户名
  Port 22                       #跳板机的端口，默认的是22
  IdentityFile ~/.ssh/id_rsa    #用来免密登录跳板机私钥的位置

# 服务器server的配置
Host server                     #服务器的标签
  HostName 210.76.200.56        #服务器的ip
  User user_server              #在服务器上的用户名，可以和跳板机上一样
  Port 52711                    #端口号，服务器端口都不是默认的22了
  ProxyCommand C:\Windows\System32\OpenSSH\ssh.exe user_jump@jump -W %h:%p
# 设置ssh代理，主要改@符号前后的信息，前面是跳板机的用户名，后面是跳板机的标签
# 若本地电脑也是Ubuntu系统，则这一行可以改为
# ProxyCommand ssh jump -W %h:%p
  IdentityFile ~/.ssh/id_rsa    #用来免密登录服务器的私钥的位置，这里为了方便我们用的是同一套公钥密钥
```

配置完成后保存即可，然后左侧 Remote - ssh 插件的位置就能看到跳板机jump和服务器server，双击即可连接。
