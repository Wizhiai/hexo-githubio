---
title: mac将app打包dmg文件
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:51:39
password:
summary:
tags:
categories:
---

首先，得到要打包的app文件，在xcode的Products下，这里可以将xcode设置成release模式，build，再去products文件夹下找到.app文件



![截屏2020-06-11 下午1.43.14](mac%E5%B0%86app%E6%89%93%E5%8C%85dmg%E6%96%87%E4%BB%B6.assets/%E6%88%AA%E5%B1%8F2020-06-11%20%E4%B8%8B%E5%8D%881.43.14.png)

**截屏2020-06-11 下午1.43.14**



在桌面新建一个文件夹，取名dmg吧，将app文件放进去。为了实现拖拽到Application的效果，需要在这个文件夹里放一个Application的替身。具体做法是cd到这个目录，建立一个软链接。



```ruby
$ cd /Users/xxxxx/Desktop/dmg
$ ln -s /Applications/   Applications
```

Ruby

打包需要用到磁盘工具。打开系统自带的磁盘工具，选择顶部的 文件-新建映象-来自文件夹的映象。然后选择到我们的那个dmg目录。如图：



![截屏2020-06-11 下午1.43.56](mac%E5%B0%86app%E6%89%93%E5%8C%85dmg%E6%96%87%E4%BB%B6.assets/%E6%88%AA%E5%B1%8F2020-06-11%20%E4%B8%8B%E5%8D%881.43.56.png)

**截屏2020-06-11 下午1.43.56**



现在就可以在当前目录生成一个dmg文件了


