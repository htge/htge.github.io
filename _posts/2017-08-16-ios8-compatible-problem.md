---
layout:     post
title:      "iOS8的一些事"
subtitle:   "整理兼容的一些坑"
date:       2017-8-16 4:00:00
author:     "Tom"
tags: [ios, Swift]
category: [ios, Swift]
comments: true
share: false
---
<h1>iOS8的一些事</h1>
虽然从iOS8走过来的，现在是iOS10。不过有些功能在兼容上确实很坑，需要注意。

<h3>1、崩溃</h3>

导入一个第三方库，编译的时候，设置的iOS版本高于当前系统版本，在iOS9可以正常运行，iOS8崩溃

<img src="/images/2017/08/ios8-crash.png" />

<h3>2、swift的ipa会增加不必要的体积</h3>

<img src="/images/2017/08/ios8-swift-dependencies.png" />

在iOS8环境下，使用swift语言编写的代码，要兼容iOS8，就需要增加一堆系统组件。没代码的ipa导出来就有6.6M，iOS9以上则正常

<h3>3、API实现缺陷</h3>

使用左滑功能时，正常情况下只要使用editActionsForRowAtIndexPath监听，就可以完成想要的功能

{% highlight objective-c %}
- (NSArray<UITableViewRowAction *> *)tableView:(UITableView *)tableView editActionsForRowAtIndexPath:(NSIndexPath *)indexPath {
	...
}
{% endhighlight %}

为了兼容iOS8，必须增加以下空实现：

{% highlight objective-c %}
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath {
    
}
{% endhighlight %}

<h3>4、下拉刷新与工具条配合的显示问题</h3>

正常情况下，SSPullToRefrseh组件可以满足一般需求，不管下拉刷新放的位置在哪都没问题。在iOS8，刚进入页面的时候，还算正常。切换tab以后就变成这样了：

<img src="/images/2017/08/ios8-layout-bug.png" />

于是要加以下兼容代码：

{% highlight objective-c %}
if (!self.tableView.isDragging && !self.tableView.decelerating && self.tableView.contentOffset.y != -64) { //修复iOS8的显示问题
    self.tableView.contentInset = UIEdgeInsetsMake(64, 0, 0, 0);
}
{% endhighlight %}

<img src="/images/2017/08/ios8-layout-fixed.png" />

<h3>5、语言检测</h3>

使用检测当前语言的api检测中文，以下写法不通用：

{% highlight objective-c %}
NSString *language = [NSLocale preferredLanguages].firstObject;
if ([language isEqualToString:@"zh-Hans-CN"]) {
    ...
}
{% endhighlight %}

因为在某些环境下会是zh-Hans-US，不一定是zh-Hans-CN。以上代码，不仅是iOS8不兼容，其他系统也可能不兼容。为了兼容所有情况，可以使用以下代码检测：

{% highlight objective-c %}
NSString *language = [NSLocale preferredLanguages].firstObject;
if ([language hasPrefix:@"zh-Hans"]) {
    ...
}
{% endhighlight %}

再过没多久，随着iOS11的推出，iOS8将成为过去。iOS8兼容的问题还不算最麻烦，早期做iOS的人都知道，iOS6～7才是最麻烦的，希望iOS11能给开发者带来更好的体验
