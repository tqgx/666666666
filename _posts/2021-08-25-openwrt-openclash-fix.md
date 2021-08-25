---
layout: post
title: "OpenClash web Ui点击更新后解除安装解决"
subtitle: "Openwrt issue, Fixed"
date: 2020-12-28
author: "NKQ"
header-img: "img/home-bg-art.jpg"
tags:
  - Fix World
  - Openwrt
---


用终端`ssh`进入路由器后台，重新安装`openclash.ipk`时提示缺少两个依赖`ruby`和`ruby-yaml`，使用以下命令安装

```linux
opkg update
opkg install ruby
opkg install ruby-yaml
```

安装过程报错提示`But that file is already provided by package  * libgmp`，导致安装失败

进入`/usr/lib`删除报错信息中提示的两个文件（忘记截图了），分别为`libgmp.so.10`和`libgmp.so.10.3.2`，重新执行安装命令，成功安装，提示

```linux
Configuring ruby
Configuring ruby-yaml
```

再次安装`openclash.ipk`也成功，但启动时又碰到新问题

```linux
2020-12-28 13:20:53 Tip: You Could Download And Re-Install The libcap & libcap-bin Library From The Address Below:
2020-12-28 13:20:53 Error: Could Not Load The Capsh Library, Please Verify The Capsh Shell Library Work Well
```

需要安装`libcap`和`libcap-bin`，执行命令

```linux
opkg install libcap
opkg install libcap-bin
```

成功安装

![img](/img/in-post/openclash-fix/libcap.png)

启动`openclash`，又报错

```linux
2020-12-28 13:44:46 Error: OpenClash Can Not Start, Please Check The Error Info And Try Again
time="2020-12-28T13:45:04+08:00" level=fatal msg="Parse config error: proxy group[0]: `url` or `interval` missing"
2020-12-28 13:44:46 Error: OpenClash Can Not Start, Try Use Raw Config Restart Again
time="2020-12-28T13:44:55+08:00" level=fatal msg="Parse config error: proxy group[0]: `url` or `interval` missing"
```

应该是配置文件有问题，修改下配置文件内容，总算是开起来了！
