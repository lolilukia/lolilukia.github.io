---
title: Centos集群搭建
date: 2017-04-04 17:16:04
tags: 技术
---
## 关闭防火墙和SELinux
```
#systemctl disable firewalld
#systemctl stop firewalld
vi /etc/sysconfig/selinux  确保SELINUX=disabled
```
然后执行
```
setenforce或reboot
```
<!--more-->
## 设置防火墙规则
```
firewall-cmd --permanent --add-service=high-availability
firewall-cmd --add-service=high-availability
```
## 主机名解析
* 修改主机名
  ```
  vi /etc/hostname
  node1
  systemctl restart network.service
  hostname
  node1  验证是否为node1
  ```
* 配置主机表
  ```
  vi /etc/hosts
  192.168.xx.xx node1
  192.168.xx.xx node2
  ```
## 配置无密访问
```
ssh-keygen -t rsa
scp /root/.ssh/id_rsa.pub root@node2:/root/.ssh/authorized_keys
ssh node2 date  //测试
```