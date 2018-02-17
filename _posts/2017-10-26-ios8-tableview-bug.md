---
layout:     post
title:      "iOS8的列表控件存在隐性bug"
subtitle:   "iOS11出来，iOS8还要继续维护？"
date:       2017-10-26 4:00:00
author:     "Tom"
tags: [ios]
category: [ios]
comments: true
share: false
---
<h1>iOS8的列表控件存在隐性bug</h1>

一直以来，一个新开发工具出来以后，旧的iOS版本不被苹果支持。去年出iOS10的时候，苹果直接去掉iOS7的任何工具。今年有点不一样，iOS11出了那么久，iOS8还在。也就是说，现在要同时考虑4版操作系统的情况，加上公司处于转型期，事情真是多得不得了。

废话不多说，下面说一下iOS8存在的一个兼容性问题。之前图方便，对象的信息处理扔在了numberOfRowsInSection的回调里面。

<pre>
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
	...
}
</pre>

在iOS9、10、11均正常，但是在iOS8下，切换页面后系统还会主动触发一次。由于方法内用了代理，导致代理被覆盖的bug。

前提是列表的对象需要设置代理，列表是不固定的。当然固定不变列表也可以把信息往numberOfRowsInSection方法写，对于有经验的开发者一般不会写固定的数组在这个方法里面。

解决方法有两种：

1、代理在其他地方设置。在[tableView reloadData]执行前处理就可以了

2、在numberOfRowsInSection内部设置代理的代码中，加一条是否已经跳转到其他页面的判断
