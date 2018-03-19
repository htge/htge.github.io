---
layout:     post
title:      "浅谈面向对象的SDK的结果处理方式"
subtitle:   "返回值、代理还是其他方法"
date:       2017-12-18 4:00:00
author:     "Tom"
tags: [sdk]
category: [sdk]
comments: true
share: false
---
<h1>浅谈面向对象的SDK的结果处理方式</h1>

<h3>前言</h3>

不管是iOS还是安卓，还是基于其他语言的SDK。面向对象，都会有一些很相似的用法。比如iOS嗲用一些接口，注册delegate，安卓调用接口，注册listener。或者注册观察者、block、直接使用接口的返回值等等方式，拿到接口调用执行后的错误码、接口需要关心的内容。每种方式各有各的特点，<b>不合理的设计回调接口，开发着使用起来会变得复杂和不方便</b>。对于异步调用的接口来讲，常见的问题是来自于Delegate和Listener。

{% highlight objective_c %}
@protocol SampleClassDelegate <NSObject>
@optional

- (void)didLogin:(NSError *)error uid:(NSString *)uid token:(NSString *)token;
- (void)didLogout:(NSError *)error;
- (void)didRegister:(NSError *)error uid:(NSString *)uid token:(NSString *)token;
- (void)didFinishedOtherAction:(NSError *)error params:(id)params);

@end

@interface SampleClass

@property (weak, nonatomic) id <SampleClassDelegate>delegate;

- (void)login:(NSString *)username password:(NSString *)password;
- (void)logout:(NSString *)uid;
- (void)register:(NSString *)username password:(NSString *)password;
- (void)otherAction:(NSString *)uid params:(id)params;

@end

{% endhighlight %}

看起来好像没什么问题，但是在某一个特殊的场景下就会需要开发者自己做一个中间件。这个中间件，要把代理的事件实现多级转发才能够完成。要做的那么复杂，SDK不改机制的情况下无法实现？是的，就是不能。在安卓和iOS，代理和Listener，设计之初，只是需要开发者设置一次代理关联到一个指定的接收器，而某些情况需要多个接收器同时接收的时候，就发现SDK的设计并不能满足这样的需求。一个很真实的例子，目前开发的大型开源框架，需要后台做匿名登录，又要保证切换到其他界面也能够与自动登录共用一个代理，前提是代理只能被一个地方使用的情况下，只能做一个监听器转发类才能完成。

<img src="/images/2017/12/delegate1.png"/>

理论上应该达到上面的效果。实际上，在切换页面后，传统的实现出现了Bug

<img src="/images/2017/12/delegate2.png"/>

应用程序的Bug修复方法

<img src="/images/2017/12/delegate3.png"/>

<h3>可行的优化方案</h3>

既然setDelegate和setListener没那么好用，那么就可以采用其他的方式解决。

简单可行的一种方式：addDelegate()和removeDelegate()

另一种iOS可行的方式：block，安卓看过去好像有这种语法，有空的时候装一个Android Studio或者Eclipse试一下。

<h3>不同结果回调方式的优缺点</h3>

* 代理，常见的异步处理结果方式，使用简单，能方便接收通知消息。如果想知道不同账号同时登录后的结果对应关系，通常情况下局限于设计，只能一个个分别处理，不能并发执行。

* Block（Swift语言叫闭包），参数作为一个函数类型定义在接口中，调用接口可以容易知道是执行了什么内容对应返回的结果。有点麻烦的地方就是对于Objective-C来讲，可能造成循环引用，内存管理问题。对于消息通知来讲，block显得不那么适合。

* 消息中心注册通知或者建立观察者，也是处理接口回调的一种方式，对于接口设计和使用来说比较不实用，这里不讨论。

* 返回值，能快速拿到所需信息的话，可以用。要是跟C语言那样做阻塞和非阻塞，用起来就没有面向对象本身的优势了。
