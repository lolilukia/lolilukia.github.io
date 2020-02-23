---
title: Linux实用指令记录
date: 2017-04-15 14:46:19
tags: 技术
---
#### 关闭SELinux
```
vi /etc/sysconfig/selinux
SELinux=disabled
```
#### 解压
```
tar -zxvf xxx.tar.gz
unzip xxx.zip
```
#### 安装工具
```
yum -y install xxx
```
<!--more-->
#### 重新启动
```
reboot
shutdown -r now
```
#### vim的使用
```
vi xxx //编辑xxx文件
按i进入insert模式
再按Esc退出，连续按两次大写Z退出vim
输入/xxx，在文中查找xxx
```
#### 开启CUDA
```
nvidia-xconfig -query-gpu-info
```
#### 设置权限
```
chmod a+x xxx
chmod -R 777 xxx //传递给子文件夹
```
#### 修改hostname
```
vi /etc/hostname
```
#### 输出当前路径
```
pwd
```
#### 安装dukto
```
cd /etc/yum.repos.d/
wget http://download.opensuse.org/repositories/home:colomboem/CentOS_7/home:colomboem.repo
yum install dukto
```
#### 传输文件夹
```
scp -r /docu/ root@node2: /opt
```
#### 修改文件（夹）名
```
mv 修改前文件（夹）名  修改后文件（夹）名 
```
#### 删除文件（夹）
```
rm -rf 文件夹名
```
#### 进入图形界面
```
startx
```
#### 挂载移动硬盘
```
安装ntfs-3g（去官网下载）
cd /mnt
mkdir ddj
fdisk -l 看移动硬盘的磁盘名称
mount -t ntfs-3g /dev/sdc /mnt/ddj
mount
df -H  查看是否成功挂载
```
#### 查找含某个关键字的文件
```
grep -r "xxx" /xxx/xxx（目录）
```
#### 修改日期
```
date -s 2016-xx-xx
```
#### 修改一个文件夹下所有文件的时间
```
touch -d 2016-xx-xx xx(文件夹路径）
```