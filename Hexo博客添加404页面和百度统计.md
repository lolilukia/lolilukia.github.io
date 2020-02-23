layout: blog
title: Hexo博客添加404页面和百度统计
date: 2017-03-23 23:23:49
tags: 博客
---
最近几天一直在下实验数据，实验暂且搁置，用了些时间在改造个人博客上。添加404页面和百度统计，工作量不大，但是非常实用。
## 添加404页面
我使用的是腾讯公益404页面，主要是宣传寻找走失儿童的，借博客的力量让更多人看到，当然自定义404页面也是差不多的道理
首先在source文件夹下，创建一个404.html文件
<!--more-->
![](https://cl.ly/2S0F113p2m2W/download/[5a57b543c07c142e018add3c226c3601]_a.png)
### 腾讯公益404
```
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
</head>
<body>

<script type="text/javascript" src="https://www.qq.com/404/search_children.js"
        charset="utf-8" homePageUrl="http://lolilukia.com"
        homePageName="Back to homepage">
</script>

</body>
</html>
```
在博客的_config.yml文件中标记防止被主题渲染
```
skip_render: 404.html
```
### 百度统计
我用的主题是Litten的yilia，在**themes/yilia**的**_config.yml**文件中，将百度统计属性开启
```
baidu_analytics: true
```
然后注册百度统计账号，获取代码插入博客代码中，并检测安装，代码格式如下：
```
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?7fc459dce0444dbf817d4fe8513d7dff";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>
```
打开**themes/yilia/layout/_partial/baidu-analytics.ejs**，插入如下格式的代码
```
<% if (theme.baidu_analytics){ %>
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?7fc459dce0444dbf817d4fe8513d7dff";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>
<% } %>
```
打开同目录下的**head.ejs**，插入
```
<%- partial('baidu_tongji') %>
```
然后clean,generate,deploy，经过20分钟之后，打开管理中心就可以看到统计结果了