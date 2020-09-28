---
title: 在页面内隐藏statusbar
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:52:36
password:
summary:
tags:
categories:
---



有的时候需要进入页面后，点击某个按钮改变界面，同时需要隐藏状态栏，这个时候就需要手动改变状态栏状态。

**一、有关状态栏的隐藏**

1.1、方式一：提示：控制器之间的状态栏不会相互影响 下面是𝑖𝑂𝑆9之后的设置，不支持𝑖𝑂𝑆9以前设备下面是iOS9之后的设置，不支持iOS9以前设备

<1>、在info.plist里面添加下面代码为YES



```
View controller-based status bar appearance
```



![1](https://wizhiai.github.io/2020/06/23/zai-ye-mian-nei-yin-cang-statusbar/1.png)

**1**



<2>、在控制器里面定义一个属性



```
@property(nonatomic,assign) BOOL statusHiden;
```

<3>、重写状态栏的一个方法



```
- (BOOL)prefersStatusBarHidden{

     return self.statusHiden;
 }
```

<4>、改变状态栏的隐藏与显示

- 隐藏状态栏



```
self.statusHiden = YES;
// 刷新状态栏
[self performSelector:@selector(setNeedsStatusBarAppearanceUpdate)];复制代码
```

- 显示状态栏



```
self.statusHiden = NO;
// 刷新状态栏
[self performSelector:@selector(setNeedsStatusBarAppearanceUpdate)];
```


