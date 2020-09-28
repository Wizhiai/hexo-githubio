---
title: view自旋转
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:52:09
password:
summary:
tags:
categories:
---

\- (**void**)rotateView:𝑈𝐼𝐼𝑚𝑎𝑔𝑒𝑉𝑖𝑒𝑤∗UIImageView∗view`

```
{
CABasicAnimation *rotationAnimation;
rotationAnimation = [CABasicAnimation animationWithKeyPath:@"transform.rotation.z"];
rotationAnimation.toValue = [NSNumber numberWithFloat:M_PI*2.0];
rotationAnimation.duration = 1;
rotationAnimation.repeatCount = HUGE_VALF;
[view.layer addAnimation:rotationAnimation forKey:@"rotationAnimation"];
}
```

使用：

loading等待图

新建一个imageview，然后

在开始等待是，使用此方法进行自旋转，产生loading视觉效果

在停止等待是，使用 [**self**.imageview removeAllAnimations];//停止动画

jsonDict = getNONullNSDictionary𝑗𝑠𝑜𝑛𝐷𝑖𝑐𝑡jsonDict;// 去除空数据

