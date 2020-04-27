---
title: SPARTA使用笔记与常见问题
date: 2020-03-30
tags: DSMC
---
记录一下在一台新机器上运行SPARTA程序可能需要经历的步骤，目前记录内容不全，随时更新。<!--more-->

## 一、编译程序

### Makeflie的修改

在修改Makefile之前，需要先安装一些必要的库/环境  

#### 安装mpi

```bash
sudo apt-get install mpich-dev
```

通过命令直接按照即可，不需要进行额外的操作

#### 安装libjpeg

为了能够在计算过程中输出流场图像，随时掌握计算发展进程，需要在编译时就链接jpeg库。安装命令

```bash
sudo apt-get install libjpeg-dev
```

#### 安装ffmpeg

为了能够在计算过程中输出流场视频，需要安装ffmpeg。安装命令

```bash
sudo apt-get install ffmpeg
```

#### 安装imagemagick

可以将导出的图像转成gif动画。安装命令

```bash
sudo apt install imagemagick-6.q16
```

可能版本有所不同，输入`convert`，根据提示来安装即可。

#### 修改Makefile文件的设置

添加SPARTA -D选项

```makefile
SPARTA_INC =-DSPARTA_GZIP -DSPARTA_JPEG -DSPARTA_FFMPEG
```

然后再Makfile文件中添加相应的信息。mpi的安装位置若为默认，则不需要指定。

```makefile
# INC = path for jpeglib.h
# PATH = path for JPEG library
# LIB = name of JPEG library

JPG_INC = -L/usr/include/
JPG_PATH = -L/usr/lib/x86_64-linux-gnu/
JPG_LIB = /usr/lib/x86_64-linux-gnu/libjpeg.so
```

不同的机器、系统版本可能有所不同，在/usr/目录下使用`find -name '*libjpeg*'`可以查找到文件`jpeglib.h`和`jpeglib.so`文件的确切位置，再进行相应的调整。

#### 编译

- 进入sparta/src文件夹
- 通过make进行编译，`make -j 12 mpi`，会得到一个可执行文件`spa_mpi`

## 运行程序

- 新建工作目录
- 将可执行程序以及需要的其他参数文件拷贝到工作目录
- 在工作目录下新建程序的输入文件
- 执行程序，`mpirun -np 12 spa_mpi <in.flie >out.txt &`
  
在远程计算的时候，有时候程序在后台会被无故杀掉，而且计算的日志文件里也没有相应的报错信息。为了避免这一情况，建议利用tmux工具。先新建一个tmux页面，然后在tmux页面中运行程序，最后退出tmux。实践证明这样是能规避上述问题的。

## 常见问题

## 当前版本BUG

这里采用的版本一般是GitHub上最新的版本，BUG指的是程序本身的缺陷，而不是由于使用时设置不恰当导致的误差。

>1. 弛豫参数只能取constant，不能取variable，否则会出现温度不能平衡的问题。输入脚本中将其设置为`collide vss air ar.vss relax constant`，由于`relax`的缺省值即为`constant`，因此，这个语句等同于`collide vss air ar.vss`。
>2. 目前最新版处理振动能时有bug，平衡状态不能保持，无论振动能选择`constant`还是`discrete`的存储方式。
