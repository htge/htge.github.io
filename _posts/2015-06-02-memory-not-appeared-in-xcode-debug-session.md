---
layout:     post
title:      "Xcode 的 debug session 无法看到内存信息的解决方法"
subtitle:   ""
date:       2015-06-02 15:03:00
author:     "Tom"
tags: [ios, debug]
category: [ios]
comments: true
share: false
---
---
layout: post
title:  "Xcode 的 debug session 无法看到内存信息的解决方法"
date:   2015-06-02 15:03:00
permalink: /XCode-Debug-FAQ-1/
categories: iOS
comments: true
---
调试某些项目的时候，在 Xcode 的基本调试信息里面不显示内存信息？这是个 bug？不对，其他项目都可以看到。到底为什么呢？<br><br>
原来，是项目中设置了一个选项。

<b>解决方法：</b><br>

1. 编辑项目的方案 scheme

    ![](/images/2015/06/0201.png)

2. 进入运行(Run) -> 诊断选项卡(Diagnostics)，去掉 `Enable Zomble Objects`

    ![](/images/2015/06/0202.png)

去掉这个选项后，再运行，debug session 终于可以显示内存信息了。

