---
title: Linux终端配置
date: 2020-03-15
tags: 
- Linux
- server
---

除了桌面操作系统图形交互界面以外，在经常接触的Linux系统（Ubuntu，deepin，WSL）中，各种终端是很常见和重要的。当然，系统自带的不是不能用，肯定能满足我们大部分需求，但如果经常用的话，自然希望能够用起来更加顺手，这里记录下自己用得上的一些更改。<!--more-->

## 更改更新源

鉴于系统默认的更新源连接不稳定，时常出现连不上的情况，首先需要更改更新源为国内镜像。推荐中科大源，在国科大校园网内更新也是不走流量的。更改方法有很多教程，例如<https://www.linuxidc.com/Linux/2018-08/153709.htm>

## zsh与ohmyzsh

参考：[zsh的安装教程](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH),[ohmyzsh的github主页](https://github.com/ohmyzsh/ohmyzsh)

无法联网的机器可以手动安装，以ohmyzsh为例：

1. 在oh-my-zsh的github主页，手动将zip包下载下来。
2. 将zip包解压，拷贝至~/.oh-my-zsh目录。此处省略拷贝的操作步骤。
3. 执行cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

### Ubuntu上的安装步骤

1 安装zsh

```shell
sudo apt install zsh
```

2 设置为默认shell

```shell
chsh -s $(which zsh)
```

3 重启终端

4 安装ohmyzsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

若提示找不到`curl`命令，就安装一下，`sudo apt install curl`

5 搞定，若想折腾花里胡哨的主题，或者各种插件，再去github主页上翻就行了，这里记录几个我自己在用的。

> oh my zsh 的自定义设置

1. 主题推荐miloshadzic或steeef
2. 自动补全
   先将插件克隆到本地

   ```bash
   git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
   ```

3. 语法高亮
   将插件库克隆到本地

   ```bash
   git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
   ```

   在·`~/.zshrc`中添加上述两个插件，然后更新`source ~/.zshrc`

   ```bash
   plugins=(zsh-autosuggestions zsh-syntax-highlighting)
   ```

4. 解决浏览Windows文件夹时，由于文件系统不同导致的文件夹会有绿色背景色的问题
   在 .zshrc 最尾部添加如下代码

   ```bash
   # Change ls colours
   LS_COLORS="ow=01;36;40" && export LS_COLORS
   # make cd use the ls colours
   zstyle ':completion:*' list-colors "${(@s.:.)LS_COLORS}"
   autoload -Uz compinit
   compinit
   ```

## tmux的安装配置

tmux就直接用命令行就能装了，这里主要是记录下一些常见的命令，主要还是用的不多。

```bash
sudo apt install tmux
```

### Sessions

a session is an independent workspace with one or more windows

`tmux` starts a new session.
`tmux new -s NAME` starts it with that name.
`tmux ls` lists the current sessions
Within tmux typing `<C-b> d` dettaches the current session
`tmux a` attaches the last session. You can use `-t` flag to specify which

### Windows

Equivalent to tabs in editors or browsers, they are visually separate parts of the same session

`<C-b> c` Creates a new window. To close it you can just terminate the shells doing `<C-d>`  
`<C-b> N` Go to the `N` th window. Note they are numbered  
`<C-b> p` Goes to the previous window  
`<C-b> n` Goes to the next window  
`<C-b> ,` Rename the current window  
`<C-b> w` List current windows  

### Panes

Like vim splits, pane let you have multiple shells in the same visual display.

`<C-b> "` Split the current pane horizontally
`<C-b> %` Split the current pane vertically
`<C-b> <direction>` Move to the pane in the specified direction. Direction here means arrow keys.
`<C-b> z` Toggle zoom for the current pane
`<C-b> [` Start scrollback. You can then press `<space>` to start a selection and `<enter>` to copy that selection.
`<C-b> <space>` Cycle through pane arrangements.
