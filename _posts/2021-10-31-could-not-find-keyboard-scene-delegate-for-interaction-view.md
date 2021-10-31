---
layout: post
title: "Could not find keyboard scene delegate for interaction view问题修复"
subtitle: "Fix"
date: 2021-10-31
author: "NKQ"
header-img: "img/home-bg-art.jpg"
tags:
 - Fix World
 - iOS
---

在开发Demo时，遇到了一个问题，iOS键盘不弹出，log中打印以下信息

> Could not find keyboard scene delegate for interaction view

由于目前大部分项目仍然兼容到iOS9，使用SceneDelegate的项目还较少，搜索了一下这个问题也没多少人碰到，简短的记录一下

SceneDelegate中有这样一个方法

```objc
- (void)scene:(UIScene *)scene willConnectToSession:(UISceneSession *)session options:(UISceneConnectionOptions *)connectionOptions;
```

一般的会先创建一个UIWindowScene对象，这里两种创建方法都可以让程序运行下去，但是他们是有区别的

```objc
UIWindowScene *windowsScene = [[UIWindowScene alloc] initWithSession:session connectionOptions:connectionOptions];
```

这种方法创建的UIWindowScene会导致键盘无法弹出，并且在log打印我遇到的信息

应当直接把参数中的UIScene转类型到UIWindowScene即可

```objc
UIWindowScene *windowsScene = (UIWindowScene *)scene;
```
