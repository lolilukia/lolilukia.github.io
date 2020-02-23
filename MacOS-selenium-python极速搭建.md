---
title: MacOS + selenium + python 极速搭建
date: 2017-03-15 10:58:23
tags: 技术
---
大四实习临离职的时候做过一周的自动化测试，用的是公司电脑里的**Eclipse**，导了几个jar包，用java写的一套自动化测试，比较入门，但是实现测试用例还是足够了的，如今为了小公主的面试，我决定试试**MacOS**的水。

不得不说，**MacOS**是真方便啊。

## 环境搭建
**MacOS**自带了**python2.7**，默认安装目录是/usr/bin/python, 还需要一个文本编辑器，用[sublime](http://www.sublimetext.com/)即可，美观高亮

![sublime](https://cl.ly/103G3c3X383L/download/[644114119faae046396e6ca98c8ffafb]_Image%202017-03-15%20at%2010.33.32.png)
<!--more-->

安装**selenium**
```
sudo easy_install selenium
```
以Firefox为例，安装[geckodriver](https://github.com/mozilla/geckodriver/releases)驱动
下载解压之后，移动到/usr/local/bin中
```
sudo mv ~/Downloads/geckodriver /usr/local/bin/
```
如果出现了以下的问题，就是没有安装**geckodriver**
>selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH.

## 简单的自动化测试脚本
打开**sublime**，键入几句简单的测试代码
```
from selenium import webdriver
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.common.keys import Keys
import time

browser = webdriver.Firefox() 
browser.get("http://www.baidu.com") 
assert "Hahaha" in browser.title
browser.close()
```
保存成**test.py**，然后在命令行中运行
```
python test.py
```
如果出现了如下的错误
>selenium.common.exceptions.WebDriverException: Message: Missing 'marionetteProtocol' field in handshake

说明**Firefox**的版本太老了，更新之后再运行，发现百度的页面打开，这个简单脚本就运行成功了。

有时间的时候再探究一下**Chrome**。
## 一个稍显复杂的例子
### 功能简介
这个脚本的作用是从豆瓣某个小组里面搜索包含‘上海’关键词的帖子，然后一分钟一刷新浏览器，出现新帖子就给目标邮箱发邮件，邮件正文内容就是帖子的链接。
### 脚本地址
[douban_script](https://github.com/lolilukia/douban_script)