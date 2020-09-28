---
title: wkwebview的坑：iOS10位分水岭，包括跨域和低版本不能再沙盒访问js，css等资源文件
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:48:10
password:
summary:
tags:
categories:
---

1：iOS10以下版本，wkwebview不允许在沙盒里访问html里的css，js等外部资源文件，需要转移到tmp文件夹下访问；

复制到tmp文件夹下，然后从tmp文件夹下访问即可

-(NSString *)spineFileMoveToTemp:(NSString* )path{

**if** !𝑝𝑎𝑡ℎ!path {

 **return** **nil**;

 }

// Files in “/temp/www” load flawlesly :)

 NSString *temDirPath =[NSString stringWithFormat:@”%@”, [NSString stringWithFormat:@”%@”, NSTemporaryDirectory]];

path = [path stringByReplacingOccurrencesOfString:@”/index.html” withString:@””];

[**self** copyFlolderFrom:path to:temDirPath];

**return** temDirPath;

}

2：iOS10 上下版本的跨域问题： Cross origin requests are only supported for HTTP.

解决方案：

WKWebViewConfiguration *configuration = [[WKWebViewConfiguration alloc] init];

 configuration.preferences.javaScriptEnabled = **YES**;

 configuration.preferences.javaScriptCanOpenWindowsAutomatically = **YES**;

 configuration.suppressesIncrementalRendering = **YES**; // 是否支持记忆读取

 [configuration.preferences setValue:@YES forKey:@”allowFileAccessFromFileURLs”];

 **if** (**@available**𝑖𝑂𝑆10.0,∗iOS10.0,∗) {

 [configuration setValue:@YES forKey:@”allowUniversalAccessFromFileURLs”];

 }

 _webView = [[WKWebView alloc] initWithFrame:**self**.view.frame configuration:configuration];

