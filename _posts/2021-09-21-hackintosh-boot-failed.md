---
layout: post
title: "黑苹果开机后出现 Default Boot Device Missing or Boot Failed"
subtitle: "fix Hackintosh error, Default Boot Device Missing or Boot Failed"
date: 2021-01-02
author: "NKQ"
header-img: "img/home-bg-art.jpg"
tags:
  - Hackintosh
---

我的电脑今天自行休班了，报了一个错误

![1](/img/in-post/hackintosh-error-boot-fail/1.jpeg)

乍一看好像是硬盘炸了……
开机按`Fn`和`F2`进去`BIOS`，发现还可以看得到硬盘，稍微放心了，应该只是引导的问题

## PE

用U盘装上`WePE`（这一步忘记截图了），在`PE`里面替换了一下前几个月备份的整个`CLOVER`文件夹，然后用`BOOTICE`来修复引导，发现无论如何都无法成功添加新的`UEFI`

尝试直接格式化`EFI`分区，在重新设置分区后，依然无法添加新的`UEFI`

思考可能是`PE`中的`BOOTICE`无法为其他的硬盘修复引导？

直接删除`CLOVER`和`APPLE`两个文件夹，先忽略`macOS`直接修复`Windows`引导，开机后再添加`macOS`引导，就像第一次安装黑苹果那样

## Windows

> 这一步新安装黑苹果的也可以参考下

`Windows`引导修复后果然开机了，打开`cmd`挂载`EFI`分区

![2](/img/in-post/hackintosh-error-boot-fail/listdisk.jpeg)

> 请忽略我，具体命令在图中有，但还是再记录一下～

```python
打开cmd后
输入diskpart来打开diskpart.exe
在弹出的新窗口中
list disk  # 查看硬盘列表
list vol  # 查看分区列表
select disk 0  # 选择磁盘0
select partition 1  # 选择分区1
assign letter=x  # 将选择的分区挂载为盘符x
```

然后就是再把之前删除掉的`CLOVER`和`APPLE`两个文件夹重新放回`EFI`

![3](/img/in-post/hackintosh-error-boot-fail/trans.jpeg)

`EFI`文件夹看起来应该是这样的，图中有部分文件并不是必须存在的，只是我没有删掉

![4](/img/in-post/hackintosh-error-boot-fail/path.jpeg)

然后，在你的`Windows`系统中打开`BOOTICE`进行`CLOVER`引导的创建与调整

![5](/img/in-post/hackintosh-error-boot-fail/bootice.jpeg)

搞好之后，重启电脑吧！

![6](/img/in-post/hackintosh-error-boot-fail/done.jpeg)

看起来没问题

## macOS

![7](/img/in-post/hackintosh-error-boot-fail/finder.png)

确实没问题