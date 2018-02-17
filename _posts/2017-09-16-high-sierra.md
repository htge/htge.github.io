---
layout:     post
title:      "苹果新系统测试版本的体验"
subtitle:   "快出正式版了"
date:       2017-9-15 4:00:00
author:     "Tom"
tags: [osx]
category: [osx]
comments: true
share: false
---
<h1>苹果新系统测试版本的体验</h1>

使用OS X High Sirra beta 版本体验了两周，说说使用感受：

<h3>1. APFS</h3>

感受与HFS+性能差异还是有的，虽然容量使用上没有减少。安装High Sierra（10.13）以后，发现有些app的大小无法计算，还没有优化得很理想。用命令行计算文件占用空间时，确实感觉性能有大幅度提升。在加密的文件系统中，体验相当不错，速度比HFS+快了不少。以往的加密文件系统，写入速度100MB/s以内，APFS可以达到200MB/s以上。

记得在OS X Sierra（10.12）的版本中出现了锁盘的问题，现在不会。

<h3>2. 多媒体新格式</h3>

操作系统可以认得出新格式的图片，但是不能导出到HEIF

<img src="/images/2017/09/heif.png" />

可以通过QuickTime保存HEVC影片，后缀还是mov。同样的片子，时长3秒。用H264压缩4秒，hevc压缩时间45秒（测试使用Intel Haswell架构）。HEVC的压缩率是H264的90%左右

<img src="/images/2017/09/h264-1080p.png" />
<img src="/images/2017/09/hevc-1080p.png" />

<h3>3. 新系统存在致命的问题</h3>

新系统占用内存很大。用了一段时间，发现一个进程特别吃内存，只能不定期重启这个进程，GM版仍然没有修复

<img src="/images/2017/09/WindowServer.png" />

重启进程以后，内存马上没有了压力

<img src="/images/2017/09/restart-WindowServer.png" />

<h3>4、还有一些小问题</h3>

使用测试版的时候多多少少还遇到一些小问题，大体上还算正常。比如说装了flash player，插件的文件浏览功能点了没反应。清理垃圾桶的时候，提示框无法消失等等小问题。这些都不算大问题，关键的问题处理了，新系统没什么好吐槽的。
