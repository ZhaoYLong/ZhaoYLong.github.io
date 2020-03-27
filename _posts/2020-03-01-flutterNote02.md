---
layout:     post
title:      Flutter学习笔记

subtitle:   第二篇
date:       2020-03-01
author:     Laqudee.Z
header-img: img/post-bg-debug.png
catalog: false
tags:
    - flutter
---

- MaterialPageRoute继承自PageRoute类是一个抽象类，表示占有整个屏幕空间的一个模态路由页面，它还定义了路由构建及切换是过渡动画的相关接口及属性。
- Material组件库提供的组件，他可以针对不同平台，实现与平台页面切换动画风格一致的路由切换动画。
  
```js
    MaterialPageRoute({
        WidgetBuilder builder, //是一个widgetBuilder类型的回调函数，作用：构建路由页面的具体内容，返回值是一个widget
        RouteSettings settings, //包含路由的配置信息，如路由名称、是否初始路由
        bool maintainState = true, // 默认情况下，当入栈一个新路由时，原来的路由仍然会被保存在内存中，若想释放其所占资源，可以设置其为false。
        bool fullscreenDialog = false, //表示新的路由页面是否是一个全屏的模态对话框
    })
```

- Navigator
  - 一个路由管理的组件，提供打开和退出路由页方法。【栈操作】，当前页位于栈顶
  
- Future push(BuildContext context, Route route)
  - 将给定的路由入栈（打开新的页面），返回值是一个Future对象，用于接收新路由出栈的返回数据。

- bool pop(BuildContext context,[result])
  - 将栈顶路由出栈，result为页面关闭时返回给上一个页面的数据，

- 命名路由
  - 为路由起名字，然后根据名字直接打开新的路由

- 路由表
  - Map<String, WidgetBuilder> routes;

- 适配

- 命名路由参数传递
  - 在routes里注册路由
  - 在路由页通过routeSetting对象获取路由参数

```js
class EchoRoute extends StatelessWidget {
    @override
    Widget build(BuilderContext context){
        //获取路由参数
        var args=ModalRoute.of(context).settings.arguments;
        //.... 省略无关代码
    }
}
```

  - 在打开路由时传递参数：
  
```js
Navigator.of(context).pushNamed("new_page", arguments:"hi");
```

- 路由生成钩子
  - 当我们需要在打开每一个路由页前判断用户登录状态，每次打开前都判断一下会很麻烦，所有有了。
  - MaterialApp有一个onGenerateRoute属性，它在打开命名路由时可能会被调用。

```js
MaterialApp(
    ...
    onGenerateRoute:(RouteSettings settings){
        return MaterialPageRoute(builder:(context){
            String routeName = settings.name;
            // 如果访问的路由页需要登录，但当前未登录，则直接返回登录页路由，
            // 引导用户登录；其它情况则正常打开路由。
        })
    }
)
//onGenerateRoute只会对命名路由生效
```



- 包管理
- 模块共享机制，Java之jar包，Android之aar包，web开发中的npm包
- 一个app的开发会依赖很多包，这些包通常都有交叉依赖关系、版本依赖关系等。
- Android之gradle管理依赖
- iOS之cocoapods
- node中的npm
- flutter的包管理

- Pub仓库
  - 是Google官方的Dart Packages仓库，类似于node中的npm仓库，Android中的jcenter
  
- 其他依赖方式
  - 本地包
  - git仓库



- 资源管理
  - flutter App 安装包中包含代码和资源（assets）两部分。
  - assets是会打包到程序安装包中的，可在运行时访问。
  - 常见类型的assets包括静态数据，配置文件，图标和图片。




- 特定平台assets
  - 设置app图标



- flutter调试器

- Dart分析器
  - 在运行代码前运行flutter analyze 测试代码,这个工具是一个静态代码检查工具，是dartanalyzer工具的一个包装，主要用于分析并帮助开发者发现可能的错误。

- Dart Observatory(语句级的单步调试和分析器)
  - observatory默认监听http://127.0.0.1:8100

- debugger()声明
  - 可以使用debugger()语句插入编程式断点，import 'dart:developer';

```js
void someFunction(double offset){
    debugger(when:offset > 30.0);
}
```


- widget 树
  - 要转储widgets树的状态，可以调用debugDumpApp().
  - 只要应用程序已经构建了至少一次（即在调用builder()之后的任何时间），可以在应用程序未处于构建阶段（即，不在build()方法内调用）的任何时间调用此方法（在调用runApp()之后）


- flutter 异常捕获

- dart单线程模型

- main函数为应用程序的入口，main函数中调用runApp()，用于启动flutter应用。runAPP()接收一个widget参数

- StatelessWidget：无状态组件。
- StatefulWidget:一个有状态的组件，这些状态在widget生命周期中可以变化
- MaterialApp是Material库中提供的Flutter APP框架，通过它可以设置应用的名称、主题、语言、首页及路由列表等。它也是一个widget

- myApp类(设置的工程名)：代表flutter应用，继承自statelesswidget类，意味着应用本身也是一个widget。flutter在构建页面时，会调用组件的build方法，widget的主要工作就是提供一个build()方法来描述如何构建UI界面（通常是通过组合、拼装其他基础widget）

- 构建UI界面的逻辑在build方法中，

- Scaffold：material库中提供的页面脚手架，提供默认的导航栏、标题和包含主屏幕widget树的body属性。

- Navigator：一个路由管理组件，提供打开和退出路由页方法。

- MaterialPageRoute继承自PageRoute类，PageRoute类是一个抽象类，表示占有整个屏幕空间的一个模态路由页面，它还定义了路由构建及切换时过渡动画的相关接口及属性。
- builder:是一个widgetBuilder类型的回调函数，作用：构建路由页面的具体内容，返回值是一个widget。
- settings：包含路由的配置信息，如路由名称、是否初始路由（首页）