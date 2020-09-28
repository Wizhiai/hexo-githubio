---
title: 刨根问底Objective－C Runtime（1）－ Self & Super
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:59:33
password:
summary:
tags:
categories:
---





转：[http://chun.tips/blog/2014/11/05/bao-gen-wen-di-objective%5Bnil%5Dc-runtime11%5Bnil%5D-self-and-super/](http://chun.tips/blog/2014/11/05/bao-gen-wen-di-objective[nil]c-runtime(1)[nil]-self-and-super/)

#### 前言

关于Objective-C Runtime一篇好的文档 : [Understanding the Objective-C Runtime](http://cocoasamurai.blogspot.jp/2010/01/understanding-objective-c-runtime.html)

译文地址为: http://blog.cocoabit.com/blog/2014/10/06/yi-li-jieobjective-cruntime/

Objective-C Runtime源码是开源的，下载地址为: http://opensource.apple.com/tarballs/objc4/

#### 习题内容

[@唐巧_boy](http://weibo.com/tangqiaoboy)在微博上分享了他们技术讨论会关于objc runtime的讨论习题内容，习题来自 *sunnyxx*, 他的博客地址为 [http://blog.sunnyxx.com](http://blog.sunnyxx.com/)。

以下是习题内容图片转自@唐巧𝑏𝑜𝑦微博图片转自@唐巧boy微博:



![img](http://106.186.113.24:8888/resource/65dc76a3jw1elvtvv093gj20yu1krtl0.jpg)

**img**



自己做完这些题之后，也顺便复习了一些Objective－C Runtime的知识，现在整理一下，分享给大家。

该笔记分为四篇：

- [刨根问底Objective－C Runtime（1）－ Self & Super](http://chun.tips/blog/2014/11/05/bao-gen-wen-di-objective[nil]c-runtime(1)[nil]-self-and-super/)
- [刨根问底Objective－C Runtime（2）－ Object & Class & Meta Class](http://chun.tips/blog/2014/11/05/bao-gen-wen-di-objective[nil]c-runtime-(2)[nil]-object-and-class-and-meta-class/)
- [刨根问底Objective－C Runtime（3）－ 消息和Category](http://chun.tips/blog/2014/11/06/bao-gen-wen-di-objective[nil]c-runtime(3)[nil]-xiao-xi-he-category/)
- [刨根问底Objective－C Runtime（4）- 成员变量与属性](http://chun.tips/blog/2014/11/08/bao-gen-wen-di-objective[nil]c-runtime(4)[nil]-cheng-yuan-bian-liang-yu-shu-xing/)

#### 刨根问底

下面的代码输出什么？



```
@implementation Son : Father



- (id)init



{



    self = [super init];



    if (self)



    {



        NSLog(@"%@", NSStringFromClass([self class]));



        NSLog(@"%@", NSStringFromClass([super class]));



    }



    return self;



}



@end
```

答案：都输出 *Son*



```
2014-11-05 11:06:18.060 Test[8566:568584] NSStringFromClass([self class]) = Son



2014-11-05 11:06:18.061 Test[8566:568584] NSStringFromClass([super class]) = Son
```

解惑：这个题目主要是考察关于objc中对 *self* 和 *super* 的理解。

self 是类的隐藏参数，指向当前调用方法的这个类的实例。而 super 是一个 Magic Keyword， 它本质是一个编译器标示符，和 self 是指向的同一个消息接受者。上面的例子不管调用[self class]还是[super class]，接受消息的对象都是当前 Son ＊xxx 这个对象。而不同的是，super是告诉编译器，调用 class 这个方法时，要去父类的方法，而不是本类里的。

当使用 self 调用方法时，会从当前类的方法列表中开始找，如果没有，就从父类中再找；而当使用 super 时，则从父类的方法列表中开始找。然后调用父类的这个方法。

真的是这样吗？继续看：

使用clang重写命令:



```
$ clang -rewrite-objc test.m
```

发现上述代码被转化为:



```
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_gm_0jk35cwn1d3326x0061qym280000gn_T_main_a5cecc_mi_0, NSStringFromClass(((Class (*)(id, SEL))(void *)objc_msgSend)((id)self, sel_registerName("class"))));







    NSLog((NSString *)&__NSConstantStringImpl__var_folders_gm_0jk35cwn1d3326x0061qym280000gn_T_main_a5cecc_mi_1, NSStringFromClass(((Class (*)(__rw_objc_super *, SEL))(void *)objc_msgSendSuper)((__rw_objc_super){ (id)self, (id)class_getSuperclass(objc_getClass("Son")) }, sel_registerName("class"))));
```

从上面的代码中，我们可以发现在调用 `[self class]` 时，会转化成 `objc_msgSend`函数。看下函数定义：



```
id objc_msgSend(id self, SEL op, ...)
```

我们把 `self` 做为第一个参数传递进去。

而在调用 `[super class]`时，会转化成 `objc_msgSendSuper`函数。看下函数定义:



```
id objc_msgSendSuper(struct objc_super *super, SEL op, ...)
```

第一个参数是 `objc_super` 这样一个结构体，其定义如下:



```
struct objc_super {



   __unsafe_unretained id receiver;



   __unsafe_unretained Class super_class;



};
```

结构体有两个成员，第一个成员是 *receiver*, 类似于上面的 objc_msgSend函数第一个参数*self* 。第二个成员是记录当前类的父类是什么。

所以，当调用 `［self class]` 时，实际先调用的是 `objc_msgSend`函数，第一个参数是 `Son`当前的这个实例，然后在 `Son`这个类里面去找 `- (Class)class`这个方法，没有，去父类 `Father`里找，也没有，最后在 `NSObject`类中发现这个方法。而 `- (Class)class`的实现就是返回*self*的类别，故上述输出结果为 *Son*。

objc Runtime开源代码对`- (Class)class`方法的实现:



```
- (Class)class {



    return object_getClass(self);



}
```

而当调用 `[super class]`时，会转换成`objc_msgSendSuper`函数。第一步先构造 *objc_super* 结构体，结构体第一个成员就是 *self* 。第二个成员是 *𝑖𝑑idclass_getSuperclass𝑜𝑏𝑗𝑐𝑔𝑒𝑡𝐶𝑙𝑎𝑠𝑠(“𝑆𝑜𝑛”objcgetClass(“Son”)* , 实际该函数输出结果为 *Father*。第二步是去 *Father*这个类里去找`- (Class)class`，没有，然后去`NSObject`类去找，找到了。最后内部是使用 `objc_msgSend(objc_super->receiver, @selector(class))`去调用，此时已经和`[self class]`调用相同了，故上述输出结果仍然返回 *Son*。


来源: LJ的博客
作者: 胡
链接: https://bolg.91youzhi.com/2020/09/07/bao-gen-wen-di-objective-c-runtime-1-self-super/
本文章著作权归作者所有，任何形式的转载都请注明出处。