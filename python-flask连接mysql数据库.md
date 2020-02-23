---
title: python flask连接mysql数据库
date: 2017-10-02 12:25:14
tags: 技术
---
flask连接mysql数据库踩了非常多的坑，后悔没有用php写后端，抛弃了熟悉的phpMyAdmin，命令行操作不熟的我选择了MySQL workbench。

### MySQL Workbench
![](https://cl.ly/0I3e1M0M2v0q/download/Image%202017-10-03%20at%2010.33.06.png)

<!--more-->
图形界面方便很多，但在刚开始建表的时候出现了这样的错误：

> Error 1005 “Can't create table (errno: 13)”

害我检查了好几遍，并没有发现什么可疑的错误，后来才知道是要赋权限：

```
sudo chown -R mysql:mysql /usr/local/mysql/data/my_database
```
其中，my_database是数据库的名称。要设计的逻辑比较简单，建表之后，就开始安装python连接mysql的驱动包。

### flask_sqlalchemy
选取mysql驱动包的过程可谓一波三折，有很多随着python版本升级而退出历史舞台的模块和写法，现在我python版本是2.7，为了以后能兼容3（当然我也不确定），我选用了flask_sqlalchemy。

#### 安装
这里还是用到超方便的工具pip，命令如下：

```
pip install mysql-python
pip install flask-sqlalchemy
```

但显然不可能这么顺遂，我在安装时报了这样的错：

> ld: library not found for -lssl
> clang: error: linker command failed with exit code 1 (use -v to see invocation)
> error: command 'cc' failed with exit status 1

我又从官网下载了源码安装包，进行编译安装，也是一样的错误。第一句好像是说openssl库找不到，我就使用brew来安装openssl。

```
sudo chown -R $(whoami) /usr/local
brew install openssl
```

可是错误还是存在，反正我是怎么也没想到，解决方案是这样的：

```
xcode-select --install
```

安装成功。

#### 配置sqlalchemy

```
#!flask/bin/python
# -*- coding: utf-8 -*-
from flask import Flask
from flask_sqlalchemy import SQLAlchemy 

app = Flask(__name__)

#配置数据库驱动
app.config['SQLALCHEMY_DATABASE_URI']='mysql://root:myadminsql@localhost:3306/tjyx' #这里登陆的是root用户，要填上自己的密码，MySQL的默认端口是3306，填上之前创建的数据库名
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN']=True #设置这一项是每次请求结束后都会自动提交数据库中的变动
db = SQLAlchemy(app) #实例化

#定义表名
class Member(db.Model):
	__tablename__ = 'member' #定义表名
	Id = db.Column(db.Integer,primary_key=True)#定义列对象
	name = db.Column(db.String(64))
	nickname = db.Column(db.String(64))
	rest_time = db.Column(db.Integer)
	integral = db.Column(db.Integer)

#执行插入操作
user = Member(name = 'Yanke', nickname = 'Tom', rest_time = 12, integral = 100)
db.session.add_all([user])  # 准备把对象写入数据库之前，先要将其添加到会话中
db.session.commit()#提交会话到数据库
```

成功插入数据库。

这一期先到这里，2小时后就开始我的国庆青岛之旅了。


