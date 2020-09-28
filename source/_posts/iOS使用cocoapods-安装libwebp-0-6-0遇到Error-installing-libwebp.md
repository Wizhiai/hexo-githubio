---
title: iOS使用cocoapods 安装libwebp 0.6.0遇到Error installing libwebp
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:53:45
password:
summary:
tags:
categories:
---



可能会遇到libwebp v0.6.0 超时问题:



```csharp
[!] Error installing libwebp
[!] /usr/bin/git clone https://chromium.googlesource.com/webm/libwebp /var/folders/p6/t42f8nmd7332018zm9m2s3d80000gn/T/d20180415-42656-1hjxh43 --template= --single-branch --depth 1 --branch v0.6.0

Cloning into '/var/folders/p6/t42f8nmd7332018zm9m2s3d80000gn/T/d20180415-42656-1hjxh43'...
fatal: unable to access 'https://chromium.googlesource.com/webm/libwebp/': Failed to connect to chromium.googlesource.com port 443: Operation timed out
```

Csharp

解决方法:
尝试过翻墙，修改host，均无效
最终，修改pod repo中libwebp的git source 地址，再执行pod install 解决，
但是我们需要有一个有效的libwebp的git仓库，在github上找到了一个`https://github.com/webmproject/libwebp.git`，可以看到`mirrored from https://chromium.googlesource.com/webm/libwebp`，而且正好有我需要的版本0.6.0, 那么我就替换为这个。
下面是步骤:

1. 查看mac中cocoapods 本地库路径:



```ruby
swaedeMacBook-Pro:alpface swae$ pod repo
```

Ruby



```dart
/Users/swae/.rbenv/versions/2.5.0/lib/ruby/gems/2.5.0/gems/cocoapods-1.4.0/lib/cocoapods/executable.rb:89: warning: Insecure world writable dir /opt in PATH, mode 040777

master
- Type: git (master)
- URL:  https://github.com/CocoaPods/Specs.git
- Path: /Users/swae/.cocoapods/repos/master
```

Dart

1. 在本地库中, 并找到对应的libwebp版本的文件



```jsx
swaedeMacBook-Pro:alpface swae$ find ~/.cocoapods/repos/master -iname libwebp
```

Jsx



```undefined
/Users/swae/.cocoapods/repos/master/Specs/1/9/2/libwebp
```

Undefined

进入libwebp目录，可以看到你的仓库中有哪些对应的版本



```ruby
swaedeMacBook-Pro:libwebp swae$ cd ~/.cocoapods/repos/master/Specs/1/9/2/libwebp
swaedeMacBook-Pro:libwebp swae$ ls -l
```

Ruby



```css
total 0
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.4.1
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.4.2
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.4.3
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.4.4
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.5.0
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.5.1
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.5.2
drwxr-xr-x  3 swae  staff  96 12  3 21:50 0.6.0
```

Css

由于SDWebImage 依赖的 libwebp版本为0.6.0，所以我们进入0.6.0中，并做修改

==**注意！！！**==，你需要看你的libwebp版本需要的是几，不一定就是0.6.0，也有可能是1.1.0等其他版本，如图：



![截屏2020-06-30 上午10.49.32](iOS%E4%BD%BF%E7%94%A8cocoapods-%E5%AE%89%E8%A3%85libwebp-0-6-0%E9%81%87%E5%88%B0Error-installing-libwebp.assets/%E6%88%AA%E5%B1%8F2020-06-30%20%E4%B8%8A%E5%8D%8810.49.32.png)

**截屏2020-06-30 上午10.49.32**





```ruby
swaedeMacBook-Pro:libwebp swae$ cd 0.6.0/
swaedeMacBook-Pro:0.6.0 swae$ ls -l
total 8
-rw-r--r--@ 1 swae  staff  1587  4 15 09:53 libwebp.podspec.json
```

Ruby

在0.6.0目录下的libwebp.podspec.json文件中修改git source



```ruby
swaedeMacBook-Pro:0.6.0 swae$ sudo vim libwebp.podspec.json
```

Ruby

找到



```bash
"source": {
"git": "https://chromium.googlesource.com/webm/libwebp",
"tag": "v0.6.0"
},
```

Bash

将其中的`"git"` 对应的url替换为`https://github.com/webmproject/libwebp.git`,并保存
最后再执行`pod install`, 完成



```ruby
swaedeMacBook-Pro:alpface swae$ pod install
```

Ruby



```csharp
/Users/swae/.rbenv/versions/2.5.0/lib/ruby/gems/2.5.0/gems/cocoapods-1.4.0/lib/cocoapods/executable.rb:89: warning: Insecure world writable dir /opt in PATH, mode 040777
Analyzing dependencies
Downloading dependencies
Installing SDWebImage (4.3.3)
Installing libwebp (0.6.0)
Generating Pods project
Integrating client project
Sending stats
Pod installation complete! There are 8 dependencies from the Podfile and 9 total pods installed.
```

