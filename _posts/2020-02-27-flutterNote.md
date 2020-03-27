---
layout:     post
title:      Flutter学习笔记

subtitle:   第一篇
date:       2020-02-27
author:     Laqudee.Z
header-img: img/post-bg-debug.png
catalog: false
tags:
    - flutter
---

### 学习组织结构

- 第一篇，入门篇（1~7），flutter出现得背景和简介、flutter得各种类型的widget以及如何构建ui。通过这篇可以使用flutter来构建ui界面

- 第二篇，进阶篇（8~14），flutter中的事件机制、动画、自定义组件、文件和网络、插件、国际化以及flutter核心原理。读完该篇可以对flutter整体构建及原理有一个深入的认识

- 第三篇，实例篇（15），构建一个完整的flutter app开发

### 唯有深入，方能浅出

- 入门篇为浅出，进阶篇为深入。
- flutter本质上是一个ui系统

### 移动开发技术简介

#### 1.1 原生开发与跨平台技术
- 原生开发
  
- 原生应用程序是指某一个移动平台（iOS或者Android）所特有的应用，使用相应平台支持的开发工具和语言，并直接调用系统提供的sdk api。     
- Android原生应用就是指使用Java或kotlin语言直接调用android sdk开发的应用程序。      
- ios使用objective-c 或 swift语言调用 ios sdk开发的应用程序。    
  
- 原生开发的优势：    
- 可访问平台全部功能；    
- 速度快、性能高、可以实现复杂动画及绘制，整体用户体验好。

- 主要缺点：    
- 平台特定，开发成本高，不同平台维护不同代码
- 内容固定，有新功能更新时只能发版；

- 跨平台技术简介

- 针对原生开发面临的问题，提出跨平台开发方案，根据其原理，只要分为三类:  
- h5+原生（Cordova、ionic、微信小程序）    
- js开发+原生渲染（react Native、weex、快应用）
- 自绘UI+原生（QT FOR mobile 、 flutter）

- DOM树与控件树

- 文档对象模型（document object model，简称dom），是w3c组织推荐的处理可扩展标志语言的标准编程接口，一种独立于平台和语言的方式访问和修改一个文档和结构。    
- 简单说就是文档树     
- 广义的dom也可以指Android中的xml布局文件对应的控件树，dom操作就是指直接来操作渲染树（或控件树）。    
- dom树和控件树是等价的概念，前者常用于web开发，后者常用于原生开发。

- 响应式编程

react中提出一个重要思想：状态改变则ui随着改变，react框架本身就是相应用户状态改变的事件而执行重构用户界面的工作，就是典型的响应式编程范式。

- react中响应式原理：   
- 开发者只需要关注状态转移（数据），当状态发生变化，react框架会自动根据新的状态重新构建ui。    
- react框架在接收到用户状态改变通知后，会根据当前渲染树，结合最新的状态，通过Diff算法，计算出树中变化的部分，然后更新变化的部分（dom操作），从而避免整棵树重构，提高性能。

- react会在dom的基础上建立一个抽象层，即虚拟dom树，对数据和状态所做出的任何变动，都会自动高效的同步到虚拟dom，最后在批量同步到真实dom中。
  
> 思考：diff操作和dom批量更新不应该是浏览器的职责吗？第三方框架中去做合不合适？

- react native 是react在原生移动应用平台的衍生产品。    

- react中虚拟dom最终会映射为浏览器dom树，    
- react native中虚拟dom会通过JavaScriptCore映射为原生控件树

- JavaScriptCore是一个js解释器，它在react native中主要有2个作用； 
  - 1.为JavaScript提供运行环境
  - 2.是JavaScript与原生应用之间的桥梁，作用和JsBridge一样。

- rn中将虚拟dom映射为原生控件的过程分为2步：  
  - 1. 布局消息传递；将虚拟dom布局信息传递给原生；
  - 2. 原生根据布局信息通过对应的原生控件渲染控件树；

- Weex

- 快应用

- 总结

- Javascript开发+原生渲染的方式主要优点：    
  - 1.采用web开发技术栈，社区庞大、上手快、开发成本相对较低     
  - 2.原生渲染，性能相较于H5提高
  - 3.动态化较好，支持热更新

- 缺点：    
  - 1.渲染时需要Javascript与原生之间通信，在有些场景如拖动可能会应为通信频繁导致卡顿。    
  - 2.js为脚本语言，执行时需要JIT（Just In Time），执行效率和AOT（Ahead Of Time）代码仍有差距。    
  - 3.等

- QT Mobile

- 跨平台技术：自绘ui+原生

- 通过在不同平台实现一个统一接口的渲染引擎来绘制ui，而不依赖系统原生控件，所以做到不同平台ui的一致性。

- flutter就是自绘ui+原生实现跨平台

- flutter面世

- flutter是Google发布的一个·用于创建跨平台、高性能移动应用的框架。

> 教材：https://book.flutterchina.club/chapter1/mobile_development_intro.html

### flutter介绍
  
- 一套代码可以同时运行在ios和android平台     
- flutter提供了丰富的组件、接口，开发者可以很快地为 Flutter添加 native扩展   
- 同时 Flutter还使用 Native引擎渲染视图，这无疑能为用户提供良好的体验。      

- 跨平台自绘引擎   

- flutter使用skia作为2d渲染引擎

- flutter默认支持iOS、Android、fuchsia（Google新的自研操作系统）三个平台。
- flutter也可支持web开发。

- flutter的高性能主要靠2点：   
  - 1. flutter app采用dart语言开发。    
  - 2.flutter使用自己的渲染引擎来绘制UI   

- 静态编译与动态编译    
  - 静态编译（程序在执行前全部被翻译为机器码），AOT；c/c++； 
  - 解释执行（一句一句边翻译边运行），JIT； JavaScript；python

- 为啥选择dart： 
  - 开发效率高，flutter在开发阶段采用JIT模式，这样就避免了每次改动都要进行编译，极大的节省了开发时间； 在发布时可以通过AOT生成高效的ARM代码以保证应用性能   
  - 高性能，
  - 快速内存分配，flutter框架使用函数式流，这使得它在很大程度上依赖于底层的内存分配器。   
  - 类型安全，dart是类型安全的语言，支持静态类型检测，所以可以在编译前发现一些类型的错误，并排除潜在问题    
  - dart团队的支持    

- flutter framework

- 一个纯dart实现的sdk，实现了一套基础库，自底向上，简单介绍：  
  - 底下2层：（foundation和animation、painting、gestures）在Google的一些视频中被合并为一个dart ui层，对应的是flutter里的```dart:ui```包，是flutter引擎暴露的底层ui库，提供动画、手势及绘制能力。      
  - rendering层，是一个抽象的布局层，依赖于dart ui层，Rendering层会构建一个UI树，当UI树有变化时，会计算出有变化的部分，然后更新UI树，最终将UI树绘制到屏幕上，这个过程类似于React中的虚拟DOM。Rendering层可以说是Flutter UI框架最核心的部分，它除了确定每个UI元素的位置、大小之外还要进行坐标变换、绘制(调用底层dart:ui)     
  - widgets层：是flutter提供的一套基础组件库，在基础组件库之上，flutter还提供了material和cuperitino两种视觉风格的组件库。```开发flutterapp大多数场景只是和这两层打交道```    

- flutter Engine

- 一个纯c++是实现的sdk，其中包括了skia引擎、dart运行时、文字排版引擎等等。在代码调用```dart:ui```库时，调用最终会走到engine层，然后实现真正的绘制逻辑。

- 老生常谈：如何学习flutter

- 资源：
  - 官网
  - 源码及注释
  - GitHub
  - gallery源码

- 社区：   
  - stackoverflow  
  - flutter中文网社区    
  - 博客    
  - medium  

### 搭建flutter开发环境

- 略过
  
### dart语言介绍
  
- dart同时借鉴了Java和JavaScript。
- dart在静态语法方面和Java非常相似，如类型定义、函数声明、泛型等；动态特性方面和Javascript很像，如函数式特性、异步支持等    
- dart具有表现力的语法，如可选命名参数、```..```（级联运算符）和```?.```(条件成员访问运算符)以及```??```(判空赋值运算符)。    
- Google想把dart打造成一门集百家之所长的编程语言  

#### 1.1变量声明
- var ：类似于Javascript中的var，可以接受任何类型的变量，但最大的不同是dart中var变量一旦赋值，类型便会确定，不能再改变其类型。   

```dart
  var t;
  t = "hi world";
  // 下面代码在dart中会报错，因为变量t的类型已经确定为String，
  // 类型一旦确定后则不能再更改其类型。
  t = 1000;
```

- dynamic 和 object 
 - ```object```是dart所有对象的根基类，也就是说所有类型都是```object```的子类（包括Function和Null），所以任何类型的数据都可以赋值给```object```声明的对象。    
 - ```dynamic```和```object```相同之处在于他们声明的变量可以在后期改变赋值类型   

```dart
  dynamic t ;
  Object x;
  t = "hi world";
  x = "hello Object";
  //下面代码没有问题
  t = 1000;
  x = 10000;
```

- dynamic 与 object 不同的是，dynamic声明的对象编译器会提供所有可能的组合，而Object声明的对象只能使用Object的属性与方法，否则编译器会报错

```dart
  dynaimc a;
  Object b;
  main() {
    a = "";
    b = "";
    printLengths();
  }
  printLengths() {
    //no warning
    print(a.length);
    //warning
    // The getter 'length' is not defined for the class 'Object'
    print(b.length);
  }
```

- final与const

- 如果您从未打算更改一个变量，那么使用 final 或 const，不是var，也不是一个类型。 一个 final 变量只能被设置一次，两者区别在于：const 变量是一个编译时常量，final变量在第一次使用时被初始化。被final或者const修饰的变量，变量类型可以省略，如：   
  
```dart
  final str = "hi world";
  const str1 = "hi world";
```

#### 1.2 函数
- dart是一种真正的面向对象的语言，所以即是函数也是对象，并且有一个类型function.   
- 函数可以赋值给变量或作为参数传递给其他函数，这是函数式编程的典型特征。

```dart
  bool isNoble(int atomicNumber){
    return _nobleGases[atomicNumber] != null;
  }
```

- dart函数声明如果没有显式声明返回值类型时会默认当作dynamic处理，注意函数返回值没有类型推断：   

```dart
  typedef bool CALLBACK();

  //不指定返回类型，此时默认为dynamic，不是bool
  isNoble(int atomicNumber) {
    return _nobleGases[atomicNumber] != null;
  }

  void test(CALLBACK cb){
    print(cb());
  }

  //报错，isNoble不是bool类型
  test(isNoble);
```

- 对于只包含一个表达式的函数，可以使用简写语法：   

```dart
  bool isNoble(int atomicNumber) => _nobleGases [atomicNUmber] != null;
```

- 函数作为变量

```dart
  var say = (str){
    print(str);
  }
  say("hello my love")
```

- 函数作为参数传递   

```dart
  void execute(var callback) {
    callback();
  }
  execute(() => print("xxx"))
```

- 可选的位置参数
  - 包装一组函数参数，用[]标记为可选的位置参数，并放在参数列表的最后面：   

```dart
  String say(String form, String msg, [String device]){
    var result = '$from says $msg';
    if(device != null){
      result = "$result with a #device";
    }
    return result;
  }
```

- 下面是一个不带可选参数调用这个函数的例子：   

```dart
  say("Bob", "Howdy");
  //输出：Bob says Howdy
```

- 下面是用第三个参数调用这个函数的例子：

```dart
  say('Bob', 'Howdy', 'smoke signal'); 
  //结果是：Bob says Howdy with a smoke signal
```

- 可选的命名参数  
  - 定义函数时，使用{param1, param2, ...},放在参数列表的最后面，用于指定命名参数。

```dart
  //设置[bold]和[hidden]标志
  void enableFlags({bool bold, bool hidden}) {
      // ... 
  }
```

- 调用函数时，可以使用指定命名参数。

```dart
  enableFlags(bold:true, hidden: false);
```

- 可选命名参数在Flutter中使用非常多。

- 注意，不能同时使用可选的位置参数和可选的命名参数


#### 1.3 异步支持
- dart类库有非常多的返回futrue或stream对象的函数，这些函数被称为异步函数：它们只会在设置好一些耗时操作之后返回，比如IO操作。而不是等到这个操作完成。

-async 和await关键词支持了异步编程，允许写出和同步代码很像的异步代码。

- Future
  - Future与JavaScript中的Promise非常相似，表示一个异步操作的最终完成（或失败）及其结果值的表示。简单来说，它就是用于处理异步操作的，异步处理成功了就执行成功的操作，异步处理失败了就捕获错误或者停止后续操作。一个Future只会对应一个结果，要么成功，要么失败。

- Future.then

```dart
Future.delayed(new Duration(seconds:2),(){
  return "hi world";
}).then((data){
  print(data);
})
```

- Future.catchError
  - 若异步任务发生错误，我们可以在catchError中捕获错误

```dart
  Future.delayed(new Duration(seconds:2), (){
    throw AssertionError("Error");
  }).then((data){
    //执行成功，走到此处
    print("success");
  }).catchError((e){
    //失败
    print(e);
  });

```

- 在本示例中，我们在异步任务中抛出了一个异常，then的回调函数将不会被执行，取而代之的是 catchError回调函数将被调用；但是，并不是只有 catchError回调才能捕获错误，then方法还有一个可选参数onError，我们也可以它来捕获异常：

```dart
Future.delayed(new Duration(seconds: 2), () {
    //return "hi world!";
    throw AssertionError("Error");
}).then((data) {
    print("success");
}, onError: (e) {
    print(e);
});
```

- Future.whenComplete

```dart
Future.delayed(new Duration(seconds: 2),(){
   //return "hi world!";
   throw AssertionError("Error");
}).then((data){
   //执行成功会走到这里 
   print(data);
}).catchError((e){
   //执行失败会走到这里   
   print(e);
}).whenComplete((){
   //无论成功或失败都会走到这里
});
```

- Future.wait 
  
```dart
Future.wait([
  // 2秒后返回结果  
  Future.delayed(new Duration(seconds: 2), () {
    return "hello";
  }),
  // 4秒后返回结果  
  Future.delayed(new Duration(seconds: 4), () {
    return " world";
  })
]).then((results){
  print(results[0]+results[1]);
}).catchError((e){
  print(e);
});

//输出：hello world
```

- Async/await
  - 和js的功能用法是一摸一样

- 回调地狱callback hell
  - 如果代码中有大量异步逻辑，并且出现大量异步任务依赖其它异步任务的结果时，必然会出现Future.then回调中套回调情况。举个例子，比如现在有个需求场景是用户先登录，登录成功后会获得用户ID，然后通过用户ID，再去请求用户个人信息，获取到用户个人信息后，为了使用方便，我们需要将其缓存在本地文件系统，代码如下：

```dart
  //先分别定义各个异步任务
  Future<String> login(String userName, String pwd){
    //用户登录
  }；
  Future<String> getUserInfo(String id){
    //获取用户信息
  }；
  Future saveUserInfo(String userInfo){
    //保存用户信息
  }；
```
- 接下来，执行整个任务流:  

```dart
  login("alice", "******").then((id){
    //登录成功后通过，id获取用户信息    
    getUserInfo(id).then((userInfo){
    //获取用户信息后保存 
    saveUserInfo(userInfo).then((){
       //保存用户信息，接下来执行其它操作
        ...
    });
  });
  })
```

#### 1.4 Stream
- Stream 也是用于接收异步事件数据，和Future不同的是，可以接收多个异步操作的结果。
- 在执行异步任务时，可以通过多次触发成功或失败事件来传递结果数据或错误异常。 Stream 常用于会多次读取数据的异步任务场景，如网络内容下载、文件读写等。

```dart
Stream.fromFutures([
   // 1秒后返回结果
  Future.delayed(new Duration(seconds: 1), () {
    return "hello 1";
  }),
  // 抛出一个异常
  Future.delayed(new Duration(seconds: 2),(){
    throw AssertionError("Error");
  }),
  // 3秒后返回结果
  Future.delayed(new Duration(seconds: 3), () {
    return "hello 3";
  })
]).listen((data){
  print(data);
}, onError:(e){
  print(e.message);
}, onDone: (){

});

//输出：
I/flutter (17666): hello 1
I/flutter (17666): Error
I/flutter (17666): hello 3
```

#### 1.5 dart和Java及JavaScript对比
> [参考资源](https://book.flutterchina.club/chapter1/dart.html)

