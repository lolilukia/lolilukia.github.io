---
title: CESM模式1.2.0环境搭建指南
date: 2017-03-16 23:28:37
tags: 技术
---
研究气象模式转眼已经有了一年多的时间，主攻的方向是CESM模式的性能优化，这套内容其实在2015年就已经发布在论坛上，现在仅作为一个搬运，充实个人博客的内容。

## 安装PGI
PGI是可以运行CESM所需的编译器的一种，还可以使用ifort，PGI新版无破解，需要购买，10.6版本有破解，但没有CUDA组件
<!--more-->
```
mkdir pgi
cd 你解压PGI安装包的目录
./install
```
这里我的安装目录是**/opt/pgi**，然后配置环境变量
```
vi ~/.bashrc
export PGI=/opt/pgi/linux86-64/10.6/  
export MANPATH=$MANPATH:$PGI/man  
export LM_LICENSE_FILE=/opt/pgi/license.dat  
export PATH=$PATH:$PGI/bin 
source ./bashrc
```
然后检测PGI是否安装成功，输入**pgf90**,如果出现**$pgf90-Warning-No files to process**即为成功
## 安装zlib
[下载传送门](http://download.csdn.net/detail/u011510965/9335797)
```
cd 解压目录
./configure --prefix=/home/ed/local
make check install
```
如果出现了**No shared library support**的报错，说明需要指定编译器，可以替换成如下的命令
```
./configure --prefix=/home/ed/local --sharedlibdir=/opt/pgi/linux86-64/15.10/lib
```
## 安装hdf5
[下载传送门](http://download.csdn.net/detail/u011510965/9335833)
```
cd 解压目录
./configure --with-zlib=/home/ed/local --prefix=/home/ed/local
make
make check
make install
```

## netcdf系列安装
netcdf从4.2就开始分开了,netcdf-c和netcdf-fortran一定要装在一起,并且使用同一个编译器,不然就会出现很难解决的错误
### netcdf
[下载传送门](http://download.csdn.net/detail/u011510965/9335765)
```
cd 解压目录
CPPFLAGS=-I/home/ed/local/include LDFLAGS=-L/home/ed/local/lib ./configure --prefix=/home/ed/local CC=pgcc FC=pgf90
make
make check
make install
```
### netcdf-c
[下载传送门](http://download.csdn.net/detail/u011510965/9336007)
```
CPPFLAGS=-I/home/ed/local/include LDFLAGS=-L/home/ed/local/lib ./configure --prefix=/home/ed/local CC=pgcc FC=pgf90
make
make check
make install
```
### netcdf-fortran
先添加一个环境变量
```
export LD_LIBRARY_PATH=/home/ed/local/lib:${LD_LIBRARY_PATH}
source ~/.bashrc
```
然后进行安装
```
CPPFLAGS=-I/home/ed/local/include LDFLAGS=-L/home/ed/local/lib ./configure --prefix=/home/ed/local CC=pgcc FC=pgf90
make
make check
make install
```
>注意：重新**make**的话，要先运行**make clean**

配置netcdf的环境变量

```
vi ~/.bashrc
export NETCDF=/home/ed/local
export PATH=$PATH:$NETCDF/bin:$NETCDF/include:$NETCDF/lib
export MANPATH=$MANPATH:$NETCDF/share/man
source ~/.bashrc
```

检测**netcdf**是否安装成功：**ncdump**
## 安装mpich
建议安装的版本是3.1.1

## 配置CESM
打开scripts/ccsm_utils/Machines,里面有个config_machines.xml打开在后面添加，按自己目录酌情修改
```
<machine MACH="newmach">                 //machine name自己取的，newmach
        <DESC>os is Linux</DESC>
        <OS>LINUX</OS>
        <COMPILERS>pgi</COMPILERS>
        <MPILIBS>mpich</MPILIBS>
        <RUNDIR>/cesm/cesm1_2_0/$CASE/run</RUNDIR>        //自己定的
        <EXEROOT>/cesm/cesm1_2_0/$CASE/bld</EXEROOT>
        <DIN_LOC_ROOT>/cesm/cesm1_2_0/inputdata</DIN_LOC_ROOT>       //文件夹事先要创建
        <DIN_LOC_ROOT_CLMFORC>/cesm/cesm1_2_0/lmwg</DIN_LOC_ROOT_CLMFORC>
        <DOUT_S_ROOT>/cesm/cesm1_2_0/archive/$CASE</DOUT_S_ROOT>
        <DOUT_L_MSROOT>/cesm/cesm1_2_0/$CASE</DOUT_L_MSROOT>              
        <CCSM_BASELINE>/cesm/cesm1_2_0/ccsm_baselines</CCSM_BASELINE>
        <CCSM_CPRNC>/cesm/cesm1_2_0/tools/cprnc</CCSM_CPRNC>
        <SUPPORTED_BY>srinathv -at- ucar.edu</SUPPORTED_BY>
        <GMAKE_J>2</GMAKE_J>            //建议填1或2
        <MAX_TASKS_PER_NODE>8</MAX_TASKS_PER_NODE>
        <PES_PER_NODE>16</PES_PER_NODE>
</machine>
```
然后打开config_compiler.xml打开在后面添加
```
<compiler MACH="newmach">
   <NETCDF_PATH>/home/ed/local</NETCDF_PATH>
   <ADD_SLIBS>-L/home/ed/local/lib -lnetcdf -lnetcdff</ADD_SLIBS>
   <MPI_PATH>/home/ed/local/mpich</MPI_PATH>
</compiler>
```
然后执行cp env_mach_specific.userdefined env_mach_specific.newmach,在后面添加
```
set NETCDF_PATH = /home/ed/local
set MPI_PATH = /home/ed/local/mpich
setenv INC_NETCDF ${NETCDF_PATH}/include
setenv LIB_NETCDF ${NETCDF_PATH}/lib
setenv INC_MPI ${MPI_PATH}/include
setenv LIB_MPI ${MPI_PATH}/lib
```
然后执行cp mkbatch.userdefined mkbatch.newmach,修改按照自己机器的情况,将两句含有mpi执行命令（mpiexec和mpirun）的其中一句解除注释，就可以了

## 运行CESM
先到**models/utils/pio**下
```
./configure
```
然后cd到**models/utils/mct**下
```
./configure CC=pgcc FC=pgf90
```
>注：如果出现C编译器的相关问题，就重装PGI

到**scripts**目录
```
./create_newcase -case /cesm/cesm1_2_0/case -mach newmach -compset X -res f19_g16
```
然后进入**case**那个目录
```
./cesm_setup
./case.clean_build   (svn :用户名：guestuser   密码：friendly)
./case.build
./case.run
```
当时摸索用了1个月的时间，希望之后的同学可以少走些弯路。
