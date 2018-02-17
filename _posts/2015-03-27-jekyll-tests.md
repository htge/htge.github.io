---
layout:     post
title:      "N个试验"
subtitle:   "Markdown 嵌套 liquid 的输出示例"
date:       2015-03-27 10:06:40
author:     "Tom"
tags: [jekyll, liquid, markdown]
category: [jekyll]
comments: true
share: false
---
这些试验，包含了 Markdown 嵌套 Liquid 语法输出到页面的真实结果，以及 Markdown 嵌套 HTML 的输出结果。需要看编译前的源代码，请直接看该项目源码 `sources` 里的 `_posts` 目录。

<font color="#0000bf"><b>实验开始：</b><br></font>
小写转大写的用法，计算文字长度：<br>

    Hello {{ 'tom' | upcase }}
    Hello tom has {{ 'tom' | size }} letters!
    Hello {{ '*tom*' | upcase }}

输出当前的年月日：<br>

    最后编译时间 {{ 'now' | date: "%Y-%-m-%-d %H:%M:%S" }}

输出原始数据：<br>
{% raw %}
    In Handlebars, {{ this }} will be HTML-escaped, but {{ that }} will not.
{% endraw %}

if/unless 语句的用法：<br>
{% if user == null %}
    Hello {{ 'Tom' }}
{% endif %}
{% unless user != null %}
    Hello {{ 'Tom' }}
{% endunless %}

case/when/else 的用法：<br>
{% assign condition = 2 %}
{% case condition %}
{% when 1 %}
    hit 1
{% when 2 or 3 %}
    hit 2 or 3
{% else %}
    default hit {{ condition }}
{% endcase %}

cycle 不分组、分两组的取值：<br>

    {% cycle 'one', 'two', 'three' %}
    {% cycle 'one', 'two', 'three' %}
    {% cycle 'one', 'two', 'three' %}
    {% cycle 'one', 'two', 'three' %}   

    {% cycle 'group 1': 'one', 'two', 'three' %}
    {% cycle 'group 1': 'one', 'two', 'three' %}
    {% cycle 'group 2': 'one', 'two', 'three' %}
    {% cycle 'group 2': 'one', 'two', 'three' %}

for 的用法：<br>
{% assign array = "1|2|3|4|5|6" | split: "|" %}
{% for item in array %}
    item = {{ item }}
{% endfor %}
{% for item in array limit:2 offset:2 %}
    {{ item }}
{% endfor %}
{% assign nCount = '4' %}
{% for i in (1..nCount) %}
    i = {{ i }}
{% endfor %}

加减乘除的基本运算：<br>
{% assign count = '8' %}
{% assign count2 = count | plus:1 %}
{% assign count3 = count | minus:2 %}
{% assign count4 = count | times:3 %}
{% assign count5 = count | divided_by:4 %}
{% assign count6 = count | divided_by:7.0 %}
    count = {{ count }} <br>
    count2 = count + 1 = {{ count2 }}
    count3 = count - 2 = {{ count3 }}
    count4 = count * 3 = {{ count4 }}
    count5 = count / 4 = {{ count5 }}
    count6 = count / 7 = {{ count6 }}

<!-- 不支持 shopify 里面的一些方法  
{% assign count7 = count6 | round:2 %}
    count7 = {{ count7 }}
-->

图片和字体：<br>
<img src="/images/test.jpg"/>

<font size='11'>font size=11</font><br>
<font color='#ff0000'>font color #ff0000</font><br>
<font size='11' color='#ff0000'>font size=11, color #ff0000</font><br>

Objective C 代码片段：<br>
{% highlight objective-c %}
[XPGWifiSDK sharedInstance];
- (int)xpgWifiSDK:(XPGWifiSDK *)sdk {
    return 0;
}
{% endhighlight %}

这个实验，到这里就全部结束了。接着，开始 github 上的博客之旅吧！
