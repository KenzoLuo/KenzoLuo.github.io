---
title: dsmcFOAM的安装与配置
date: 2020-04-27
tags: 
- Linux
- DSMC
---

记录dsmcFoam的安装配置过程<!--more-->

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

然后开始编译，由于编译计算量较大，若有条件可以通过选项`-j 8`使其通过8个进程（当然其他进程数也是可以的）来并行加速这一过程。（并行加速了时间也还是挺长的，先去吃个饭吧）

```shell
./Allwmake -j 8
```

编译完成后，可以输入`blockMesh`试试能否正确输出相应的信息。

## 安装dsmcFOAM

dsmcFOAM是MicroNanoFlow OpenFOAM (MNF)的一个功能模块，还有mdFOAM模块等。基于OpenFOAM 2.4.0版本开发的。

1. 下载[Third-Party-2.4.0](http://downloads.sourceforge.net/foam/ThirdParty-2.4.0.tgz?use_mirror=mesh)
2. 下载[MicroNanoFlows OpenFOAM-2.4.0](https://github.com/MicroNanoFlows/OpenFOAM-2.4.0-MNF/archive/master.zip)
3. 解压缩文件。
   1. 在根目录下新建目录`OpenFOAM`，并将下载好的文件拷贝到此目录下
   2. 解压缩`tar xzvf ThirdParty-2.4.0.tgz`,`tar xzvf dsmcFoam+code.tar.gz`
   3. 重命名文件夹`mv ThirdParty-2.4.0 ThirdParty-2.4.0-MNF`,`mv OpenFOAM-2.4.0-MNF-1.0.1 OpenFOAM-2.4.0-MNF`
   4. 修改环境变量

   ```bash
   vim ~/.bashrc
   ```

   在文件末尾添加

   ```bash
   alias of24MNF='source $HOME/OpenFOAM/OpenFOAM-2.4.0-MNF/etc/bashrc; export WM_NCOMPPROCS=[8]'
   ```

   然后更新环境变量`source ~/.bashrc`，完成后可以在命令行输入`of24MNF`测试下，如果提示未找到相应命令则说明环境变量没设置好。
   5. 开始编译
      1. 切换到ThirdParty目录下
      2. 在终端输入`./makeCmake`，完成后再执行`wmSET $FOAM_SETTINGS`
      3. 在终端输入`./Allwmake`
      4. 在终端输入`./makeParaView4`，构建paraFoam模块
      5. ParaView构建后，它会提示你设置3个系统环境变量、ParaView_Dir、PATH和PV_PLUGIN_PATH，可以复制下来，粘贴到第4步的别名alias后面，最后可能看起来像下面这样

    ```bashrc
    alias of24MNF='source $HOME/OpenFOAM/OpenFOAM-2.4.0-MNF/etc/bashrc;
    export WM_NCOMPPROCS=[8]
    export ParaView_DIR=/home/kenzo/OpenFOAM/ThirdParty-2.4.0-MNF/platforms/linux64GccMNF/ParaView-4.1.0
    export PATH=$ParaView_DIR/bin:$PATH
    export PV_PLUGIN_PATH=$FOAM_LIBBIN/paraview-4.1'
   ```

      6. 输入别名`of24MNF`来加载这些环境变量
      7. 切换到`OpenFOAM-2.4.0-MNF`目录，在终端输入`./Allwmake`来安装OpenFOAM-2.4.0和MNF组件。(漫长的安装过程...)
