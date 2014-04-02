---
layout: post
title: "Android 项目模块化"
date: 2014-04-02 21:02:43 +0800
comments: true
categories: 
---
伴随移动设备的智能化，移动应用呈现出爆发式的增长。当应用多了以后，应用框架也就自然而然的提上日程。框架设计的第一步就是模块化，实现公共模块和应用模块，模块之间通过接口通信，然后才可能去更进一步的考虑依赖注入，MVC，领域和事件模型。Android在模块化方面继承了Java的特点，允许以jar包的方式组织和发布模块，不同的是Android仅仅支持静态链接，不支持动态载入模块。Java应用在运行时通过ClassPath找到依赖的模块，而Android则必须在编译期将依赖的模块打包进apk。


为了将依赖的jar打包进apk，不仅仅要将jar加入依赖列表，还需要指定jar是Exportable的。在Eclipse下，要将jar的路径加入Build Path，还需要将其加入Export Path。用maven来进行模块构建以及依赖管理时，要将依赖的项目加入dependencies列表，并且注意scope不能指定为provided。

如果打包好的apk在运行时报错，可以分析一下logcat的输出，如果发现NoClassDefFound之类的错误，则说明有一些依赖的包没有Export（另一个引起这个错误的原因是代码混淆没有配置好，这个以后再讨论）。有时候子类的定义没有找到的话，也会报父类找不到，所以需要仔细的看日志，找到根源的类。

有时候Eclipse编译出来的apk可用，maven编译的不可用，这个时候可以考虑解压缩apk和反编译dex，找到maven编译的apk缺少的包和类。

以上两个方法一般都可以解决Android项目模块化时遇到的问题，再强调一下，仔细的分析log，不能忽视任何警告和错误信息。
