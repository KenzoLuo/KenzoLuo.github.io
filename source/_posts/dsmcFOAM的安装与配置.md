---
title: dsmcFOAM的安装与配置
date: 2020-04-27
tags: 
- Linux
- DSMC
---

## 安装openFOAM

由于实验室的服务器大多不能连接外网，这里选择离线安装的方式。参考[官网给出的方法](https://openfoam.org/download/7-source/)

- 下载源文件

<http://dl.openfoam.org/source/7>
<http://dl.openfoam.org/third-party/7>

- 解压缩

创建目录`openfoam`作为安装目录，并将下载好的源文件移动到该目录下，然后通过命令来解压文件

```shell
wget -O - http://dl.openfoam.org/source/7 | tar xvz
wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
```

然后重命名目录

```shell
mv OpenFOAM-7-version-7 OpenFOAM-7
mv ThirdParty-7-version-7 ThirdParty-7
```

- 安装一些必要的组件

安装OpenFOAM需要的组件

```shell
sudo apt-get install build-essential flex bison git-core cmake zlib1g-dev libboost-system-dev libboost-thread-dev libopenmpi-dev openmpi-bin gnuplot libreadline-dev libncurses-dev libxt-dev
```

安装ParaView需要的组件

```shell
sudo apt-get install libqt5x11extras5-dev libxt-dev qt5-default qttools5-dev curl
```

- 配置环境变量

```shell
vim $HOME/.bashrc
```

在文件末尾添加

```shell
source $HOME/openfoam/OpenFOAM-7/etc/bashrc
```

保存退出后，`source $HOME/.bashrc`使其生效，重启终端也有同样的效果

- 编译OpenFOAM

进入OpenFOAM文件

```shell
cd ~/openfoam/OpenFOAM-7
```

然后开始编译，由于编译计算量较大，若有条件可以通过选项`-j 8`使其通过8个进程（当然其他进程数也是可以的）来并行加速这一过程。

```shell
./Allwmake -j 8
```

编译完成后，可以输入`blockMesh`试试能否正确输出相应的信息。
