---
title: 设置view随键盘升高，点击切换密码模式时，底部暴露出黑色背景
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 14:02:03
password:
summary:
tags:
categories:
---



这种一般设置下当前视图view的背景颜色为与当前颜色匹配即可。

比如，navagationcontroller下的一个controller的背景颜色为黑色，设置_navigationController.view.backgroundColor = [UIColor whiteColor];即可。

其实这个涉及到一个app的view图层关系

最底下的是window，再就是你的根视图，比如tabbarcontroller，或者navagaitoncontroller

，上层就是常用的controller视图了，你当前视图背景颜色不对，那肯定是下层的背景颜色与你的预期不符，去修改即可。

