---
title: 阿里云云虚拟主机配置https
date: 2018-01-24 15:26:38
tags: 技术
---

最近开发的微信小程序后台要求使用https，为了节省成本而租用的云虚拟主机，配置https可谓大费周折，还差点因为https配置不成功，放弃了6元/年的云虚拟主机。

无法远程登录的云虚拟主机，据说只能借助CDN开半程的https，以下是具体的配置方法

### 准备工作

首先，需要有一个在阿里云租用的云虚拟主机，并且绑定了域名。

然后购买CDN流量包（建站版）

![](https://podcast.webview.tech/lolilukia/20200220US3hO6.png)

<!--more-->

### 购买证书

https的高级证书很昂贵，但是为了推广https，会有基本版的免费证书，在**安全（云盾）->CA证书服务（数据安全）**，点击**购买证书**

![](https://podcast.webview.tech/lolilukia/20200220VDRK9j.png)

先选**Symantec**，然后选择**1个域名**，然后再选**免费型DV SSL**，选择立即购买。

输入域名进行基本的配置之后，就等待系统自动签发。

![](https://podcast.webview.tech/lolilukia/20200220mvjHj2.png)

点击证书详情，选择云产品推送，推送CDN和负载均衡

### 配置域名

产品与服务中选择**CDN**，然后选择**添加域名**

![](https://podcast.webview.tech/lolilukia/20200220VxP1QJ.png)

**这里需要注意的是，一定是80端口，因为虚拟主机的443端口已被占用。**

然后进入域名的配置，点击**HTTPS设置**的修改配置：

![](https://podcast.webview.tech/lolilukia/20200220t8YzXr.png)

选择开启https之后，选择上传证书，然后公钥和私钥部分会自动填全

**注意跳转类型选择HTTP->HTTPS**

HTTP/2我也开启了，似乎只是个优化

然后复制标题下面的CNAME

![](https://podcast.webview.tech/lolilukia/20200220b4bjDT.png)

选择**域名**->**解析**

![](https://podcast.webview.tech/lolilukia/20200220ldBFmS.png)

然后到了解析设置，选择**添加解析**

添加一条如下的解析：

![](https://podcast.webview.tech/lolilukia/20200220V0hkrh.png)

其中**CNAME**是**记录类型**，**www**是**主机记录**，**记录值**是你刚刚复制的内容，其余均选择默认即可

等待几分钟刷新之后，就可以看到绿色的https啦～