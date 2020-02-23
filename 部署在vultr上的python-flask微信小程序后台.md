---
title: 部署在vultr上的python flask微信小程序后台
date: 2017-09-28 14:05:17
tags: 技术
---
最近打算给羽毛球协会写个微信小程序，希望能由此推动无纸化进程。从大一开始，亲历了这个社团非常多的变化，从刚开始固定人数，现场填写表格到后来的微信群，群里报名统计，但干事的工作还是比较繁重的，其实这也是很多社团面临的问题。

目前还处于程序开发的初级阶段，后台选用了python的flask框架，尽管php是世界上最好的语言，人生苦短，我用python。

<!--more-->

首先面对的一个问题就是外部接口，突然想起为了科学上网搭建的vps，值得担忧的问题是vultr的ping太高，处理同时报名活动这种高并发事件可能会有问题。但是还是先把功能完成，后续成熟时再租阿里云腾讯云啥的好了。

## 本地环境
闲话少叙，我们直接进入本地环境的搭建环节。由于我是OSX系统，自带了python 2.7，另外由于我也经常写python，所以pip也是必不可少的。

#### 安装virtualenv

下载链接 [**virtualenv**](https://pypi.python.org/pypi/virtualenv)

其中我选择的是python wheel, 安装命令如下：

```
sudo -H pip install virtualenv-15.1.0-py2.py3-none-any.whl
```

#### 新建项目

新建项目并安装flask包

```
mkdir test_api
cd test_api
virtualenv flask
flask/bin/pip install flask
```

回到test_api目录，新建一个初始文件**app.py**，是一个hello world的简单示例：

```
#!flask/bin/python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return "Hello, World!"

if __name__ == '__main__':
    app.run(debug=True)
```

然后就可以在本地看到这个api返回的hello world了：

```
chmod a+x app.py
./app.py
```
第一次使用这个框架，我的做法是在本地调试，调试完成后再部署到服务器上，可以想到服务器返回结果可能会比较慢。

## 服务器环境

我们最终的目的还是要将上述开发好的源码部署到服务器上，我选用的是现成的vultr，预装的是centos系统（只是因为我对centos比较熟悉，但其实Ubuntu类库更多也更主流）

首先我们连接到远程服务器，搭建一下python的环境

#### python环境搭建

下载地址：[**python**](https://www.python.org/downloads/source/)

我这里依旧选择了python2.7，懒得在本地更新到python3，两者的写法也有很大的区别。

解压安装

```
xz -d Python.xz
tar -xvf Python.tar
cd Python
./configure
make
make install
```

配置环境变量

```
vi ~/.bashrc
加入一行
export PATH="$PATH:/usr/local/bin/python"
返回
source ~/.bashrc
```

输入python,出现相关信息说明安装完毕。

#### 安装pip和virtualenv

```
yum install python-pip python-virtualenv
```

关闭端口8080

```
iptables -A INPUT -p tcp --destination-port 8080 -j DROP
```

和本地差不多的步骤

```
mkdir test_api
cd test_api
virtualenv flask
source flask/bin/activate
cd flask
pip install gunicorn
pip install flask
```
新建文件

```
vi app.py
```
给出vultr官网的一个例子，粘贴到app.py中：

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, Vultr!'
```

生成页面，其中2代表的是服务器CPU核心数的2倍的值，我的CPU core是1，所以这里就是2，冒号前面是py文件的名称，后面永远都是app:

```
gunicorn -w 2 app:app &
```

安装nginx，nginx支持多种配置，暂时只有这一个app，因此我就修改了默认配置:

```
deactivate
yum install nginx
vi /etc/nginx/nginx.conf
```

将以下的内容粘贴进**location /**后面的一对大括号里：

```
proxy_set_header HOST $host;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_pass http://localhost:8000/;
```

检测一下nginx的语法是否正确：

```
nginx -t -c /etc/nginx/nginx.conf
```

启动nginx:

```
/usr/sbin/nginx -c /etc/nginx/nginx.conf
```

如修改了nginx.conf，可以重新加载：

```
nginx -s reload
```

然后输入**http:/vps的ip/**就可以看到**Hello Vultr**的字样啦

好了，这一期先写到这里，后面有时间跟进会发布新的博客（然而应该并没有谁来看）。
