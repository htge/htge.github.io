---
layout:     post
title:      "关于做SDK的时候遇到的一些问题"
subtitle:   "遇到麻烦只是偶然？"
date:       2017-7-22 4:00:00
author:     "Tom"
tags: [ios]
category: [ios]
comments: true
share: false
---
<h1>关于做SDK的时候遇到的一些问题</h1>

这个月，遇到一些小问题。这些问题说严重，也不算严重。主要还是开发者体验，上一个版本做的确实不够。主要体现在两个方面：一个是多个SDK集成时符号表的链接问题，另一个是Xcode自带framework（动态库）发布的问题。

<h2>Link问题</h2>

这个得从客户还SDK说起。客户用2.04版本的SDK完全正常，换2.07版本的SDK出现必现崩溃，客户就肯定把矛头指向我们。因为只换了我们的SDK才会出现的，这个问题只能我们查。当时APP交互图没有进展，就花时间去找问题。半天多的时间，查到还真是用我们的SDK的方法起冲突。原因很简单，我们的cJSON做了定制，结构体不同。

<img src="/images/2017/07/cjson-problem.png" />

为什么别的库调用的方法会链接到我们的库上呢？下面我就简单说一下iOS下的Link机制：

* Link顺序

哪个库放在最顶上，就表示这个库暴露出来的符号会最先被链接。

<img src="/images/2017/07/link-order.png" />

这个库符号和别的库冲突，不会报错，而是会优先使用先链接的库暴露的符号。

* 解决方法

1、动态库加排除符号列表

<img src="/images/2017/07/unexpected-symbol.png" />

2、在开源代码库的基础上，定制的库对应的方法、结构体名称全部重新命名

<h2>动态库发布</h2>

用动态库封装的理由，很简单。一个SDK内部做些什么，不会受到其他SDK暴露的符号所影响，能最大限度保证SDK内部可以稳定的执行。静态库使用的内部方法发生冲突，就容易导致SDK运行出现异常。随着SDK的规模越来越大，内部集成的东西越来越多，怎样才能保证SDK内部运行的稳定性，成为首要处理的问题。还好，在iOS8的时候，苹果就给开发者提供了Cocoa Touch Framework工程。现在手机最新版本为iOS10，基本上所有的应用最低系统要求都会升到iOS8，动态库从现在到以后，只会越来越广泛的应用在项目中。

动态库编译，模拟器和真机的架构，要像静态库能合并起来，开发的时候就不需要根据调试环境不停更换文件。合并了以后，对于AppStore来说，这样的动态库是不适合上架的。所以我们要对上架app对应的动态库做一点简单处理<a href="http://ikennd.ac/blog/2015/02/stripping-unwanted-architectures-from-dynamic-libraries-in-xcode/">点击查看代码原始出处</a>：

{% highlight shell %}
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"

# This script loops through the frameworks embedded in the application and
# removes unused architectures.
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"

EXTRACTED_ARCHS=()

for ARCH in $ARCHS
do
echo "Extracting $ARCH from $FRAMEWORK_EXECUTABLE_NAME"
lipo -extract "$ARCH" "$FRAMEWORK_EXECUTABLE_PATH" -o "$FRAMEWORK_EXECUTABLE_PATH-$ARCH"
EXTRACTED_ARCHS+=("$FRAMEWORK_EXECUTABLE_PATH-$ARCH")
done

echo "Merging extracted architectures: ${ARCHS}"
lipo -o "$FRAMEWORK_EXECUTABLE_PATH-merged" -create "${EXTRACTED_ARCHS[@]}"
rm "${EXTRACTED_ARCHS[@]}"

echo "Replacing original executable with thinned version"
rm "$FRAMEWORK_EXECUTABLE_PATH"
mv "$FRAMEWORK_EXECUTABLE_PATH-merged" "$FRAMEWORK_EXECUTABLE_PATH"

done
{% endhighlight %}

大概意思，就是在生成后的app中，把framework内部可执行文件的i386、x86_64架构移除。然而这样还不够，还有一步，查看发布的动态库内部Info.plist是否正确。上架不成功，会收到以下信息：

<pre>
A nested bundle contains simulator platform listed in CFBundleSupportedPlatforms Info.plist key.
</pre>

APP和APP内部的所有framework包含的Info.plist文件中CFBundleSupportedPlatforms的值必须是iphoneos，而不是simulator。Info.plist只有上架会检查，调试、inhouse不会检查这个文件的一些字段。当然Info.plist也不能错的太离谱，比如说用其他项目改一改编译的拿过来用，即使打包ipa，也是装不上的。

<img src="/images/2017/07/valid-info.png" />
