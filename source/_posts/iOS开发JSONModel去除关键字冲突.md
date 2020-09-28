---
title: iOS开发JSONModel去除关键字冲突
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:50:24
password:
summary:
tags:
categories:
---



情景模拟,如后台返回JSON数据，如（图一）:



![img](iOS%E5%BC%80%E5%8F%91JSONModel%E5%8E%BB%E9%99%A4%E5%85%B3%E9%94%AE%E5%AD%97%E5%86%B2%E7%AA%81.assets/5115882-c831959723874a1e.png)

**img**



图一

创建一个用于接收数据的model类，如图:



![img](iOS%E5%BC%80%E5%8F%91JSONModel%E5%8E%BB%E9%99%A4%E5%85%B3%E9%94%AE%E5%AD%97%E5%86%B2%E7%AA%81.assets/5115882-541ccab4b1264950.png)

**img**



订单order模型类.h



![img](iOS%E5%BC%80%E5%8F%91JSONModel%E5%8E%BB%E9%99%A4%E5%85%B3%E9%94%AE%E5%AD%97%E5%86%B2%E7%AA%81.assets/5115882-e8e7d48f1823ceb2.png)

**img**



订单order模型类.m

在相应的网络请求回调处，接收JSON数据，如图:



![5115882-51c68e371a365936.png (iOS%E5%BC%80%E5%8F%91JSONModel%E5%8E%BB%E9%99%A4%E5%85%B3%E9%94%AE%E5%AD%97%E5%86%B2%E7%AA%81.assets/5115882-51c68e371a365936.png)](https://upload-images.jianshu.io/upload_images/5115882-51c68e371a365936.png)

**5115882-51c68e371a365936.png (480×128)**



model接收JSON数据

这样order模型中就获取到对应的数据了，需要用时，直接调用model就行了；

