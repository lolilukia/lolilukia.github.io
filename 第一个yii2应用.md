---
title: 第一个yii2应用
date: 2017-04-18 00:21:38
tags: 技术
---
最近在做一个网站，打算用vue写前端，用yii2写后端，vue的安装过程比较顺利，记录一下Mac OSX搭建yii2框架的过程。
## 安装Composer
[Composer下载地址](http://getcomposer.org/)
或者键入以下命令
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```
<!--more-->
## 创建应用模版
我安装的是高级应用模版
```
php composer.phar create-project --prefer-dist --stability=dev yiisoft/yii2-app-advanced /Users/lolilukia/test
```
## 初始化
在初始化之前，我就想运行，结果出现了如下的错误
> Could not open input file: yii

需要初始化一下应用
```
sudo php init
```
发现需要一个Token, Token是从github上获取的
[Token生成地址]( https://github.com/settings/tokens)
## 运行
运行这里费了很多周折，首先，输入指令
```
php yii serve
```
发现出现了如下的报错
> Document root "/Users/lolilukia/test/console/web" does not exist

一看确实没有，但是我们的主页分明是frontend/web里的index.php啊
输入以下指令
```
php yii serve --docroot = "frontend/web"
```
发现出现了如下的错误
> Document root "1" does not exist

这个问题非常的诡异，经过思考，我把指令改成了这样
```
php yii serve --docroot='frontend/web'
```
非常优秀，终于出现了**http://localhost:8080 is token by another process**的字样，默认端口是8080，可我的vue应用占用了8080端口，于是又改了个参数：
```
php yii serve --docroot='frontend/web' --port=8888
```
终于成功啦！当然我是为了做后端项目，后端的运行地址是'backend/web'，看来以后也可以尝试用它来做全站项目。