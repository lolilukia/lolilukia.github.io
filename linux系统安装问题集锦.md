---
title: linux系统安装问题集锦
date: 2017-03-28 15:22:32
tags: 技术
---
## Centos/win双系统win10引导丢失
```
vi /boot/grub2/grub.cfg
```
加入
```
###BEGIN /etc/grub.d/40_custom###
menuentry "windows10"{
	insmod part_msdos
	insmod ntfs
	set root = '(hd0, msdos1)'
	chainloader+1
}
```
<!--more-->
## 安装系统时出现UEFI问题
分区时将**/boot/efi**添加进去
## 系统启动时出现dracut-initqueue问题
> warning: /dev/root does not exist
```
dracut:/# cd /dev
dracut:/dev #ls
```
将启动项改为
```
hd: /dev/sdb4 xxxx quiet
```
## 系统安装时将引导程序装在了U盘上
用U盘启动，在终端中输入
```
$sudo grub
grub> find /boot/grub/stage1
```
然后记录下打印的结果
修改menulist文件，将记录下的结果放在**root**的后面

## 使用ssh-add
出现**Could not open a connection**
```
ssh-agent bash --login -i
ssh-add ~/.ssh/id_rsa
```
## 使用yum安装
出现**Another app is currently holding the yumlock**
```
rm -f /var/run/yum.pid
```
## Can't locate Switch.pm in @INC
```
yum install perl-Switch
```
## Host key verification failed
```
ssh-keygen -R xxx(要连接的服务器IP)
```