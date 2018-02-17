---
layout:     post
title:      "C/C++ 编程需要的注意的问题"
subtitle:   ""
date:       2015-12-28 10:00:00
author:     "Tom"
tags: [ios, c, crash, optimize]
category: [ios]
comments: true
share: false
---
<h1><a name="top">摘要</a></h1>

C/C++ 编程，只会简单的编码，是远远不够的。底层的代码，如果有一点瑕疵，运行的工程中就有可能出现不稳定的情况。一个正常的代码或者存储区域被破坏以后，这些数据是不可以恢复的。一个问题会引起其他的问题，也就是连锁反应。调试器报了一个错，但有可能会无从查起。一个程序片段，为什么一种写法速度快，一种写法速度慢，哪种代码的写法能够表现最优？所以针对这些问题，下面做一个系统性的总结。

<hr><br>

<h1><a name="context">目录</a></h1>

<h2>1. C/C++ 代码崩溃的原因</h2>

* [引用空指针](#11)
* [引用野指针](#12)
* [缓冲区溢出](#13)
* [内存对齐](#14)
* [资源互斥，共享变量的访问和更改](#15)

<h2>2. 理顺代码片段，提高性能</h2>

* [内存分配和释放](#21)
* [代码执行顺序、结构优化](#22)
* [改进算法，提高计算速度](#23)

<hr><br>

<h3><a name="11">引用空指针</a></h3>

引用了指针地址为 0x0。

{% highlight c %}
char *p = NULL;
p[0] = 0;
{% endhighlight %}

典型的问题，地址 0x0000000 是不可写的，但代码给这个地址设置了值。

{% highlight c %}
typedef struct _test {
    int a;
} test;
test *p = NULL;
p->a = 0;
{% endhighlight %}

同理，空指针的结构体，也是不可以操作的。
总结：遇到这种情况，一般会遇到编译器报 SIGSEGV/SIGABRT，指向地址为 0 至一个比较小的值，这种情况还是相对容易通过调试定位的。

[返回目录](#context)

<hr><br>

<h3><a name="12">引用野指针</a></h3>

引用了为一个错误的地址，但这个地址看似是正确的。

{% highlight c %}
char *p = malloc(1);
free(p);
p[0] = 0;
{% endhighlight %}

这段代码有两个问题，一个是 malloc() 返回值没有判断空指针，另一个是指针被释放，仍然访问这个指针是很危险的。正确的做法是：

{% highlight c %}
char *p = malloc(1);
if(NULL != p) {
    free(p); 
    //如果下面的代码可能用到指针 p，需要将 p 设置为空
    p = NULL;
}
{% endhighlight %}

遇到野指针错误，比较难查，不知道可能崩溃的地方在哪。开发的时候一定要注意避免野指针的使用。

说到野指针，还有一种情况需要注意，就是<strong>没有初始化的指针也有可能是野指针</strong>。

{% highlight c %}
char *p;
if(NULL != p) {
    free(p);
    p = NULL;
}
{% endhighlight %}

这段代码问题也很明显，一个是 p 没初始化，NULL != p 的条件可能无效；另一个是没有分配的内存不可以使用 free()。

[返回目录](#context)

<hr><br>

<h3><a name="13">缓冲区溢出</a></h3>

引用了超出范围的地址，也是引用野指针的一种。在字符串中，缓冲区溢出容易引起危险。

{% highlight c %}
char p[4];
strcpy(p, "1234");
printf("%s", p);
{% endhighlight %}

这段代码好像是对的。这里可能被忽略的一个问题，就是尾0。缓冲区 4 个字节，在字符串复制的过程中已经溢出。"1234" 是 5 个字节，大于 p 定义的 4 字节。这样就会出错。由于 strcpy 复制了超出定义范围的数据，破坏了程序正常的运行数据。这样还可能导致 printf 执行错误，或者 printf 打印的内容不正确。

解决方法：

{% highlight c %}
char p[4];
strncpy(p, "1234", sizeof(p)-1);
printf("%s", p);
{% endhighlight %}

[返回目录](#context)

<hr><br>

<h3><a name="14">内存对齐</a></h3>

很多新手刚开始开发底层的代码，都会忽略一个问题，那就是内存对齐。不正确的内存对齐，会导致内存越界。这个越界，开发者往往认为写的代码没有问题，这样崩溃的问题就有可能不了了之。

{% highlight c %}
//1 字节对齐
#pragma pack(1)

struct {
    uint8_t a;
    uint32_t b;
} test = { 0 };
test.b = 0x12345678;
{% endhighlight %}

这段代码看似没有问题，实际跑的时候会报错。不同系统，对内存对齐的要求不一样。iOS 系统下，要求至少 2 字节对齐。而安卓、windows 系统下，要求 4 字节对齐。

{% highlight c %}
//4 字节对齐
#pragma pack(4)

struct {
    uint8_t a;
    uint32_t b;
} test = { 0 };
test.b = 0x12345678;
{% endhighlight %}

这样就没有问题了。

[返回目录](#context)

<hr><br>

<h3><a name="15">资源互斥，共享变量的访问和更改</a></h3>

资源互斥类型：

* P/V 互斥量<br>
* 自旋锁<br>
* 互斥锁<br>
* 读写锁

实践过程中，常用的锁为互斥锁和读写锁。读写锁为自旋锁的加强版，可以兼顾性能的同时，比互斥锁更快，效率更高。

多线程访问临界区的互斥代码：

{% highlight c %}
#include <pthread/pthread.h>
static pthread_mutex_t mutex_handler = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&mutex_handler);
//访问临界区，临界区资源可安全读写
pthread_mutex_unlock(&mutex_handler);
{% endhighlight %}

自己实现也可以，保证每次进入临界区，只有一处的代码片段就可以了。

[返回目录](#context)

<hr><br>

<h3><a name="21">内存分配和释放</a></h3>

内存分配和释放有以下几种方式：

* [全局分配](#211)<br>
* [局部栈分配](#212)<br>
* [局部堆分配](#213)

这些分配方式有什么区别？

* 全局分配是固定的一块内存，在程序退出的时候不会被释放<br>
* 局部栈分配速度优于堆分配，但只可以是固定的长度，不需要手动释放内存，系统能自动回收<br>
* 局部堆分配可以动态管理内存，比较灵活，但速度较慢，需要手动回收分配的内存。

<h4><a name="211">全局分配</a></h4>

变量在程序一启动，就分配好这块内存

{% highlight c %}
#include <stdio.h>
typedef struct {
    int a;
} test;
char p[16];
int a;
test b;
/*
 * 前面加了 static
 * 只能在当前源码文件随意使用，不可以在其他源码文件使用
 */
static char p2[16];
void main() {
    /*
     * 方法内分配的固定内存区域
     * 不管哪个线程访问这个方法，指向的内存地址都是一致的
     * 其他方法使用了同样名字的变量，不会互相冲突
     */
    static char p3[16];
}
{% endhighlight %}

<h4><a name="212">局部栈分配</a></h4>

在其他地方准备调用了 main() 的时候，系统才会自动分配函数对应的栈内存。执行过程离开了 main()，对应的栈内存由系统自动回收。

{% highlight c %}
#include <stdio.h>
typedef struct {
    int a;
} test;
int main() {
    char p[16];
    int a;
    test b;

    return 0;
}
{% endhighlight %}

<h4><a name="213">局部堆分配</a></h4>

在程序执行的过程中，通过代码向系统请求分配一块堆内存、使堆内存和释放堆内存。

{% highlight c %}
#include <stdio.h>
typedef struct {
    int a;
} test;
int main () {
    test *p = malloc(sizeof(test));
    //分配完成后，即可使用
    if(NULL != p) {
        p->a = 0;
        printf("p->a = %i", p->a);
        //用完 p 记得释放
        free(p);
        p = NULL;
    }
    return 0;
}
{% endhighlight %}

[返回目录](#context)

<hr><br>

<h3><a name="22">代码执行顺序、结构优化</a></h3>

代码执行顺序和结构，与执行效率紧密相关。

执行效率高的代码，主要体现在：

* 中间过程尽量精简
* 没有多余重复的计算

现在 iPhone 的 CPU 性能已经很强大了。早期有一个代码片段，执行获取设备列表和下载配置，60 个设备，可以让 iPhone 5s 的 CPU 执行 5-10 秒，CPU 每一秒都占用 200%，太不可思议了。优化后，同样的操作，2 秒内执行完成，排除服务器查询时间消耗 1 秒，CPU 总消耗在 70% 以内。

下面是早期代码片段中的一个真实的例子，为了说明问题，这里简化过程。一段代码执行的过程，需要先执行获取字符串、解析字符串、写入文件，按照执行顺序，应该是这样的<strong>（先不考虑内存释放）</strong>：

{% highlight c %}
typedef struct {
    //结构体定义省略
} json_parse_result;
int GetChar(char **pp_buf) {
    //执行过程省略，0 成功，其他失败
}
int ParseChar(const char *p_buf, json_parse_result *parse_result) {
    //执行过程省略，0 成功，其他失败
}
int WriteStringToFile(const char *p_buf, const char *file_path) {
    //执行过程省略，0 成功，其他失败
}
int main() {
    char *buf = NULL;
    const char *file_path = 一个合法路径;
    json_parse_result parse_ret = { 0 };
    if(GetChar(&buf)) {
        printf("GetChar() failed");
        return -1;
    }
    if(ParseChar(buf, &parse_ret)) {
        printf("ParseChar() failed");
        return -1;
    }
    //解析 parse_ret 的过程略
    if(WriteStringToFile(buf, file_path)) {
        printf("WriteStringToFile(buf, file_path)");
        return -1;
    }
    return 0;    
}
{% endhighlight %}

过去是这样实现的

{% highlight c %}
typedef struct {
    //结构体定义省略
} json_parse_result;
int GetJsonParseResult(json_parse_result *parse_result) {
    //执行过程省略，0 成功，其他失败，中间可以得到一串原始 string
}
int GetStringFromJson(const json_parse_result *parse_result, char **pp_buf) {
    //执行过程省略，0 成功，其他失败
}
int WriteStringToFile(const char *p_buf, const char *file_path) {
    //执行过程省略，0 成功，其他失败
}
int WriteJsonToFile(const json_parse_result *parse_result, const char *file_path) {
    char *buf = NULL;
    if(GetStringFromJson(parse_result, &buf)) {
        return -1;
    }
    return WriteStringToFile(buf, file_path);
}
int main() {
    const char *file_path = 一个合法路径;
    json_parse_result parse_ret = { 0 };
    if(GetJsonParseResult(&parse_ret)) {
        printf("GetJsonParseResult() failed");
        return -1;
    }
    if(WriteJsonToFile(&parse_ret, file_path)) {
        printf("WriteJsonToFile() failed");
        return -1;
    }
    return 0;
}
{% endhighlight %}

这两套代码看过去最后一套代码会更简洁，但下面的代码执行效率会低得多。中间过程可以获取到写入文件的字符串，后面的代码是先把字符串编码了对象，接着再用对象编码字符串。重复的计算，导致 CPU 的性能浪费了很多。

<hr><br>

<h3><a name="23">改进算法，提高计算速度</a></h3>

通过算法提高性能，不管是在服务器，还是在客户端，都有显著的意义。通过算法改善性能，可以节省硬件开销，达到理想的运算效果。国外知名的 Google 搜索，没有算法的支撑，是不能在短短零点几秒，就可以从如此庞大的信息系统中，取出正确的结果的。现在，我也清楚的明白所谓冒泡算法等基础算法，存在的意义。

举一个最简单的例子，2 的 1000 次方，自己实现：

{% highlight c%}
int i;
double ret = 1.0;
for(i=0; i!=1000; i++) {
    ret *= 2;
}
{% endhighlight %}

用系统的算法：

{% highlight c %}
#include <math.h>
double ret = pow(2, 1000);
{% endhighlight %}

测完发现系统的算法消耗 0.038ms，传统的写法消耗 0.01ms。看来，系统自带的方法，也未必效率最大化。

当然，在系统可以承受的范围内，我们也是可以接受的。在性能过剩的时代，如果只做小于 100 次计算，大可不必去在意这个 0.03ms。假如这个计算，重复 10000 次，计算结果就可以差 300ms，可以感觉卡顿很严重了。所以，做的计算量越大，算法的改进就显得作用越来越大。

<hr><br>

<h1><a name="end">结语</a></h1>

底层开发，是有很多经验需要积累和学习的。有了这些基础知识，编程的过程中就可以清除相当多的障碍。更多的技巧，只靠这些是远远不够的。我们还需要有更好用的工具，帮助我们能够更好的排错，能做出更多有用、更有意义的事情来。
