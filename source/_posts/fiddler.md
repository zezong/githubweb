---
title: Fiddler 调试代理工具使用
date: 2017-12-03 16:55:43
tags:
---

Fiddler是一个著名的调试代理工具，它不仅能解析HTTP，而且还能解析加密的HTTPS流量

软件官网
https://www.telerik.com/fiddler

### 移动端抓包
Fiddler不但能截获各种浏览器发出的HTTP请求, 也可以截获各种智能手机发出的HTTP/HTTPS请求。
Fiddler能捕获IOS,Andriod,WinPhone,设备发出的请求，同理，也可以截获IPad, MacBook的等设备发出的HTTP/HTTPS。
前提条件是：安装Fiddler的机器，跟Iphone 在同一个网络里， 否则IPhone不能把HTTP发送到Fiddler的机器上来。

具体操作步骤如下：

#### Fiddler设置
打开Fiddler, Tools-> Fiddler Options。（配置完后记得要重启Fiddler）.

选中"Allow remote computers to connect". 是允许别的机器把HTTP/HTTPS请求发送到Fiddler上来

#### 移动端设置
假如安装了Fiddler的机器的IP地址是:192.168.0.1
打开IPhone 的Safari, 访问 http://192.168.0.1:8888， 点"FiddlerRoot certificate" 然后安装证书

打开IPhone, 找到你的网络连接， 打开HTTP代理， 输入Fiddler所在机器的IP地址(比如:192.168.0.1) 以及Fiddler的端口号8888

设置完成后Fiddler可看到移动端所有的请求