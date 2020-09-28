---
title: centos 更换mysql编码，以便支持emoji表情
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 14:00:11
password:
summary:
tags:
categories:
---

1. 更改 `/etc/mysql/my.cnf`, 添加如下内容:



```csharp
[client]
default-character-set = utf8mb4
[mysql]
default-character-set = utf8mb4
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4'
```

Csharp

1. 重启mysql
   `service mysql restart`
2. 修改表支持utf8mb4



```undefined
ALTER TABLE 表名(自行更换)  CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

Undefined

4.即可支持utf8mb4 快笑起来吧！

