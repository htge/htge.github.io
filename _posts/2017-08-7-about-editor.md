---
layout:     post
title:      "iOS文本编辑器方法的奇特经历"
subtitle:   "奇葩的兼容性问题"
date:       2017-8-8 4:00:00
author:     "Tom"
tags: [ios]
category: [ios]
comments: true
share: false
---
<h1>iOS文本编辑控件</h1>
iOS文本编辑控件，主要有两种：UITextView和UITextField。为UITextView添加灰色提示文字（Placeholder），做输入检测的时候，在iOS9.3出现的一种情况：

{% highlight objective-c %}
- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text {
	...
}
{% endhighlight %}

这个方法在iOS9.3，只要键盘上点了联想文字，就不会触发以上代理方法，在iOS10则没有这个问题。检测不到就会发生以下情况：

<img src="/images/2017/08/textview-bug.png" />

{% highlight objective-c %}
- (void)textViewDidChange:(UITextView *)textView {
	...
}
{% endhighlight %}

于是添加多一条代理实现，问题得到解决。
