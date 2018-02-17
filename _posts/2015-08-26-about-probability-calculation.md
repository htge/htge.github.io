---
layout:     post
title:      "一个有趣的计算 － 非法字符出现错误的概率是多少"
subtitle:   ""
date:       2015-08-26 18:00:00
author:     "Tom"
tags: [math]
category: [math]
comments: true
share: false
---
有一个同事问我，clientId 里面有 3 个错误的字符，出现错误的概率是多少？

一开始我没有仔细想，知道 debug 的时候错误的概率是挺高的。我只知道，clientId 随机生成的部分是由 20 个字符组成。每个字符，都是通过字符表随机抽取。字符表有大概 72 个字节，为了计算方便，就以 72 计算。

一个字符抽到不是非法的字符，概率是 1/24。那么 20 个字节呢，概率肯定大于 1/24。

于是，随便想想，概率可能是比 20/24 小一点，如果 25 个字符，岂不是概率大于 1？这不可能。

这个同事没有得到具体的数据，于是就叫上周围的同事，一起算。起初，一个同事给出答案是 3^20/72^20，另一个同事给出 0.02。我给了一个结果，0.57，他们觉得太大了，不可能。于是写了一个 app ......

{% highlight cpp %}
static int table_size = 72;
byte asciitable[table_size];
            
memset(asciitable, '+', 3);
memset(asciitable+3, 'p', sizeof(asciitable)-3);
                    
uint64_t testresult = 0;
uint64_t testcount = 10000;
uint64_t testrepeat = 10000;
double successRatio = 0;
                                    
srand((unsigned int)time(NULL));
                                        
for(uint64_t i=0; i<testrepeat; i++)
{
    for(uint64_t j=0; j<testcount; j++)
    {
        bool isFailed = false;
        for(int k=0; k<20; k++)
        {
            char c = asciitable[rand()%table_size];
            if(c == '+')
            {
                isFailed = true;
            }
        }
        if(false == isFailed)
        {
            testresult++;
        }
    }
}

successRatio = ((double)testresult)/testcount/testrepeat;
printf("The result is:\n\ntested count: %llu\nprobability:%lf", testresult, successRatio);
{% endhighlight %}

运行以后，有了答案。因为 3 个非法字符，在当前场景的概率下，的确能够大幅度的增加错误的概率！！不可思议！下面是 1 亿份样本中，执行成功的结果：

    tested count: 42689727
    probability:0.426897

失败的概率就是 1-0.43=0.57。有兴趣的朋友还可以修改代码增加样本。样本数越大，执行的结果越接近 (69/72)^20。
