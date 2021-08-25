---
layout: post
title: "Typecho出现Database Query Error解决方法"
subtitle: "typecho建站, Blog"
date: 2020-10-06
author: "NKQ"
header-img: "img/home-bg-art.jpg"
tags: []
---


> 小问题

在我上传我的博客内容时，Typecho报错

```
Database Query Error
```

查询后发现是文章中含有表情符号`emoji`，而数据库的编码(UTF-8)并不支持表情符号写入，需要更改数据表编码为`utf8mb4`，建议将所有数据表的编码都更改为`utf8mb4`

```mysql
alter table 数据表名 convert to character set utf8mb4 collate utf8mb4_unicode_ci;
```

可以使用以下两条命令查看所有数据表名称

```mysql
use typecho;
show tables;
```

最后不要忘记修改配置文件中数据库的参数，更改`charset`项目为`utf8mb4`

```
vi config.inc.php
```

至此，我的问题得到了解决

关联阅读：[MySQL中的UTF-8](https://my.oschina.net/xsh1208/blog/1052781)


