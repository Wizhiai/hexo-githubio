---
title: click the area around the prompt view to hide the promot view
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-23 17:14:30
password:
summary:
tags:
categories:
---

// 点击提示框视图以外的其他地方时隐藏弹框
-(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{

    CGPoint point = [[touches anyObject] locationInView:self];
    point = [self.alertView.layer convertPoint:point fromLayer:self.layer];
    if (![self.alertView.layer containsPoint:point]) {
        self.hidden = YES;
    }

其中。alertview为提示view，改为你的提示框即可。

![截屏2020-04-23 下午5.26.47](click-the-area-around-the-prompt-view-to-hide-the-promot-view/%E6%88%AA%E5%B1%8F2020-04-23%20%E4%B8%8B%E5%8D%885.26.47.png)

此为获取到非提示框区域需要执行的事件，可以改为你的。

![截屏2020-04-23 下午5.26.47](click-the-area-around-the-prompt-view-to-hide-the-promot-view/%E6%88%AA%E5%B1%8F2020-04-23%20%E4%B8%8B%E5%8D%885.26.47-7634116.png)

