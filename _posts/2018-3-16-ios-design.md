---
layout:     post
title:      "对MVVM、VIPER的初步理解"
subtitle:   "什么样的需求使用哪种模式"
date:       2018-3-15 4:00:00
author:     "Tom"
tags: [ios]
category: [ios]
comments: true
share: false
---
<h1>对MVVM、VIPER的初步理解</h1>

<h3>前言</h3>

今天谈谈界面的设计模式。网上有很多这样的文章，但是很多文章都讲不清楚所以然，抽象到理解都觉得困难。花了不少心思在理解这些模式，结合代码，就容易理解了。

<h3>MVC</h3>

Model-View-Controller

默认根据Xcode模版创建继承UIViewController以后的结果，就是MVC设计模式。

UIViewController的接口，包含了view属性。继承UIViewController类的对象，自带了view属性，可以这样操作一个视图：

{% highlight objective-c %}
@interface ViewController : UIViewController
@end

@implementation ViewController

- (void)viewDidLoad {
    UIView *newView = [[UIView alloc] init];
    [self.view addSubView:newView];
}

@end
{% endhighlight %}

因为视图View包含在控制器Controller里面，模式一目了然。但是Model是什么呢？有人说是持久化。按照上面的代码，做一个界面，为了能够在手机屏幕上能简单实现显示内容和交互的效果，最基本界面不含持久化，还有一个模式叫MVCS。只是通过UIViewController创建，里面还要通过模型实现很多的业务逻辑。

<h3>MVVM</h3>

Model-View-ViewModel

控制器没了？其实这种模式控制器依然存在。基于MVC的基础上，利用Reactive Cocoa把子视图和数据双向绑定，业务逻辑都放在viewModel上实现。

这样就多了一个类，viewModel类

<h3>VIPER</h3>

基于MVVM模式的viewModel再细分为Interactor、Presenter、Entity、Route。一个组件包含至少5个类

<h4>View</h4>

负责控制器初始化，控件数据绑定，事件绑定，界面细节调整。交互逻辑交给Presenter层处理

<h4>Interactor</h4>

原始数据包装为Presenter层识别的通用数据格式。也可以更新数据，做拆包。

<h4>Presenter</h4>

与View、Interactor、Route三者协调的组件，多组件之间通信

<h4>Entity</h4>

通过网络或者数据库获取原始数据。可选做一层结构体的封装，做数据缓存等其他高级管理模式。也可以更新数据，发给服务器或者修改数据库信息。

<h4>Routing</h4>

负责界面跳转，可通过URL或者原始方式跳转。URL的方式能方便解耦，对控制器关系的梳理有一定的帮助。特别是乐高的URL注册方式，开发者规范编写代码，后期维护看配置，一共有哪些控制器，对应什么样的树形结构一目了然。

![](/images/2018/03/VIPER.png "VIPER")

优点：非常适合复杂的界面，低耦合性，可测试性强<br>
缺点：实现内部通信机制繁琐

<h3>总结</h3>

布局设计模式的选择取决于界面复杂度，不同架构不同情况的可维护性是不一样的。当然，不熟悉VIPER模式，不熟悉ReactiveCocoa，代码写得不管怎么简洁，要理深入解都不是一件容易的事情。熟悉以后，对于复杂的界面，VIPER可维护性才会更有优势。
