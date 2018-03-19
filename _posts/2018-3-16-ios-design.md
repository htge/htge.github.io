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

今天谈谈界面的设计模式。了解设计模式，看几篇博客都不是那么轻易能下定论的，得一步步变为实践才行

<h3>MVC</h3>

Model-View-Controller

用户操作的视图关联到控制器Controller里面，视图调用的事件传递给控制器
Controller负责处理业务逻辑，操作Model层
View会依赖Model的数据，目前都不用这种模式，用起来太繁琐

<h3>MVP</h3>

Controller当成Presenter。View可选依赖Model的数据，一般是不会用View与Model做数据关联的。由Presenter协调View与Model之间的关系。对于简单的界面来讲，逻辑清晰，是目前广泛采用的模式。复杂的界面，Presenter就显得过于庞大了，可维护性降低

![](/images/2018/03/MVP.png "MVP")

<h3>MVVM</h3>

Model-View-ViewModel

在MVP的基础上，视图和模型，做了数据双向绑定(binder)，业务逻辑都放在viewModel上实现。由于数据绑定的关系，viewModel可以不直接操作view，直接向Model拿数据就可以更新视图的内容。视图状态多了，需要绑定的值也就特别多。复杂的数据绑定关系，变得难以调试和维护

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
