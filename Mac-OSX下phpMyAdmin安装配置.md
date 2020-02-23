---
title: Mac OSX下phpMyAdmin安装配置
date: 2017-05-07 14:26:31
tags: 技术
---
## phpMyAdmin
为了连接mysql数据库和php代码，我使用了phpMyAdmin，对于一个初出茅庐的新手来说，phpMyAdmin的安装配置稍显复杂，参考了网上一些博客，终于能够正常使用。
### 下载地址
[phpMyAdmin下载地址](https://www.phpmyadmin.net/)
<!--more-->
### 安装配置
由于phpMyAdmin的运行需要借助Apache，因此我们需要将压缩包解压到Apache的服务器根目录下，在OSX系统中，这个位置是**/Library/WebServer/Documents**，点击Finder的前往文件夹就可以访问
除此之外，我们需要修改一下Apache中的配置文件，用同样的方法进入**/etc/apache2/**中，打开httpd.conf，找到这句
>    DirectoryIndex index.html

在后面加上index.php
再加上一句
```
SetHandler application/x-httpd-php
```
接下来打开phpMyAdmin文件夹中的libraries里的config.default.php,进行如下的修改：
```
$cfg['PmaAbsoluteUri'] = 'http://localhost/phpmyadmin/';
```
这句话是你在浏览器中键入访问phpmyadmin的地址
```
$cfg['blowfish_secret'] = 'xxxxxx';
```
这句话是一个类似身份验证的东西，一定要填，不然会报错
```
$cfg['Servers'][$i]['host'] = '127.0.0.1';
```
你数据库的密码
```
$cfg['Servers'][$i]['password'] = 'xxxxxx';
$cfg['Servers'][$i]['hide_db'] = 'information_schema';
```
需要修改的部分就到这里，然后拷贝一份到phpmyadmin下面，名称改为config.inc.php
### 问题回顾
在浏览器中输入**http://localhost/phpmyadmin**应该就能显示phpMyAdmin的登录页面了，但是在登录过程中，可能会出现这样的错误：
> 1862, Your password has expired. To log in you must change it using a client that supports expired passwords.

这说明mysql的密码过期了，这种情况一般发生在刚安装mysql之后，使用初始密码登录的情况，那么我们就需要修改mysql的密码：
终端输入：
	```
	cd /usr/local/mysql/bin/
	```
然后使用管理员权限登录 
```
sudo su
```
登录mysql 
```
mysql -u root -p
```
输入原始密码，然后修改密码：
```
SET PASSWORD = PASSWORD('xxxxxxxxx');
```



