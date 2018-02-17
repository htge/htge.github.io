---
layout:     post
title:      "在网上创建可以下载 iOS app 链接的方法"
subtitle:   "IPA通过第三方平台发布方法"
date:       2015-04-15 17:00:35
author:     "Tom"
tags: [ios, ipa]
category: [ios]
comments: true
share: false
---
写了一个 iOS app，用 inhouse 证书打包好生成一个 ipa， 怎么才能通过一个网页的链接去安装这个 ipa 呢？<font color="%ff0000">当然，直接放 ipa 链接是不行的。</font>正因为如此，才需要寻找其他方法。<br><br>
stackoverflow 的原文是这样写的： <a href="http://stackoverflow.com/questions/23561370/download-and-install-an-ipa-from-url-on-ios">打开该问题</a><br>
需要先下载一个 <a href="http://www.hanchorllc.com/2010/08/24/introducing-ios-beta-builder/">Beta Builder</a> 软件辅助生成 mainfest.plist。生成完成后，把 ipa 文件和 mainfest 放在指定的网址中，就可以工作了。<br><br>
<font color="#ff0000">注意：mainfest 在网站上，路径必须是使用以 `https` 开头的链接，否则手机点开链接时，会提示`无法安装应用程序因为http://xxx.xxx.xxx证书无效`的情况。</font><br><br>
<hr><h3>下面提供一些例子，供大家参考</h3>
以 WiFiDemo.ipa 为例，ipa 的 bundle id 为 com.xtremeprog.WiFiDemo，版本号为 1.1。下面提供 iOS 设备安装 app 的链接，`点击链接后，设备会弹出对应安装提示`。<br><br>
<font color="#ff0000">注意：以下链接仅限 iOS 设备可以访问。其他平台的情况下，会出现无响应的情况属正常现象。服务器相关搭建测试请自行完成</font>

<pre>
itms-services://?action=download-manifest&amp;url=https://xxx/xxx.plist
</pre>

1. 使用 https 路径提供 ipa 的配置文件样本<br>
<a href="https://htge.github.io/WiFiDemo/manifest.plist">点击下载配置文件</a>

2. mainfest 版本号与 ipa 不一致的情况<br>
<a href="https://htge.github.io/WiFiDemo/manifest_3.plist">点击下载配置文件</a>

3. mainfest 的 bundle id 与 ipa 不一致的情况<br>
<a href="https://htge.github.io/WiFiDemo/manifest_4.plist">点击下载配置文件</a>
