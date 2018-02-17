---
layout:     post
title:      "运行已有的 android 工程问题汇总"
subtitle:   "调试安卓工程的时候遇到的一些问题"
date:       2015-04-22 14:13:00
author:     "Tom"
tags: [android, debug]
category: [android]
comments: true
share: false
---
已经装好 eclipse mobile for android，以及对应的 android sdk、jre 后，我们就可以开发安卓程序了。在导入别人正常的工程中，往往会出现各种问题，怎么解决？<br><br>
1、遇到某些项目会出现中文乱码的情况：<br>

解决方法：打开项目属性，`Text file encoding` 选 `Other`，填写 GBK，确定<br>
<br>
2、菜单里的`Build Project`显示为灰色不可点击，`command + B` 编译时提示 <font color='#ff0000'>The project was not built since its build path is incomplete</font> 等问题

解决方法：打开项目属性，左边选 `Android`，右边`Project Build Target`选择一个要编译的 Android SDK，确定<br><br>
解决这些问题，安卓 app 的编译就解决了。运行的话，可以使安卓机也可以是虚拟机：<br><br>
▪ 使用安卓机，启动开发者模式，连接数据线就可以运行；<br>
▪ 使用虚拟机，创建一个虚拟设备或使用一个现有的虚拟设备也可以运行。<br><br>
如果还有遇到其他问题，以后会在这里补充。

