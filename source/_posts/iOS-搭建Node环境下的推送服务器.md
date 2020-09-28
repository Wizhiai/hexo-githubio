---
title: iOS 搭建Node环境下的推送服务器
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:54:45
password:
summary:
tags:
categories:
---



先说注意点：

1：开发环境下要配置developer的.p12证书，生产环境的时候在换掉。

2：证书目录要用绝对的项目路径，建议使用__dirname;

代码如下：

**`const** apn = require‘𝑎𝑝𝑛′‘apn′;`

```
**var** path=require('path');
// pfx:path.join(__dirname,'../keys/product.p12'),//证书路径
// weatcher.p12
// production:true //是否是生产环境
**const** options = {
pfx:path.join(__dirname,'../keys/weatcherAPNS.p12'),//证书路径
passphrase:"123",//证书解压密码
// production:true //是否是生产环境
production:**false** //是否是生产环境
};
path.join(__dirname,'../keys/getui.p12')
**var** apnProvider = **new** apn.Provider(options);
**const** note = **new** apn.Notification();
note.expiry = Math.floor(Date.now() / 1000) + 3600; // Expires 1 hour from now.
note.badge = 1;
note.sound = "ping.aiff";
note.alert = "\uD83D\uDCE7 \u2709 You have a new message";//待推送显示内容
note.payload = {'messageFrom': 'John Appleseed'};
note.topic = "weatcherNotice";//app包名
note.compiled = **null**;
**var** apns = {
sendToSingle : **function**(deviceToken,message){
```

 `note.alert = message.message ;`

 `console.log( message.message);`

 `console.log( note);`

 `apnProvider.send(note, deviceToken).then( (result) => {`

 `console.info(result);`

 `console.info(JSON.stringify(result));`

```
});
}
}
module.exports = apns;
```

