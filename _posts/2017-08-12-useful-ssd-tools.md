---
layout:     post
title:      "苹果电脑固态硬盘常用检测工具"
subtitle:   "时常关注固态硬盘是否健康"
date:       2017-8-11 4:00:00
author:     "Tom"
tags: [bash]
category: [bash]
comments: true
share: false
---
<h1>苹果电脑固态硬盘常用检测工具</h1>
固态硬盘相对来说是一种不怎么耐用的磁盘，定期关注固态硬盘的损耗，让自己的磁盘健康始终在可控的范围内

在osx下，我们首先需要通过<a href="https://brew.sh">Homebrew</a>安装smartmontools

{% highlight bash %}
brew install smartmontools
{% endhighlight %}

第一次使用，需要打开电脑中，第一个硬盘的查看权

{% highlight bash %}
smartctl --smart=on --saveauto=on /dev/rdisk0
{% endhighlight %}

查看固态硬盘对应的<a href="https://zh.wikipedia.org/wiki/S.M.A.R.T.">S.M.A.R.T</a>状态

{% highlight bash %}
smartctl -a disk0
{% endhighlight %}

查看<a href="https://zh.wikipedia.org/wiki/S.M.A.R.T.">S.M.A.R.T</a>状态，一眼还看不出固态硬盘的损耗情况，这时就得写一个脚本去帮我搞定剩余的计算工作

{% highlight bash %}
smartctl --smart=on --saveauto=on /dev/rdisk0 > /dev/null

SMART_INFO=$(smartctl -a disk0)
MODEL_LINE=$(echo "$SMART_INFO" | grep "Device Model:")
LINE=$(echo "$SMART_INFO" | grep Wear_Leveling_Count)
FAIL=$(echo "$LINE" | awk '{print $9}')
VAL=$(echo "$LINE" | awk '{print $10}')
AVG=$[VAL % 65536]
MAX=$[VAL / 65536 % 65536]
MIN=$[VAL / 65536 / 65536 % 65536]

echo "${MODEL_LINE}"
echo "需要更换：${FAIL}"
echo "当前状态："
echo "最小计数${MIN}, 最大计数${MAX}, 平均计数${AVG}"
echo "\033[31m注：以上数据只支持苹果原装的固态硬盘"
{% endhighlight %}
