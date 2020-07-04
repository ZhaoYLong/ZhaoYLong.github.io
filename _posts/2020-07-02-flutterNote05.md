---
layout:     post
title:      Flutter学习笔记

subtitle:   第五篇
date:       2020-07-01
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - flutter
---

> 主要学习布局类组件

- 线性布局（Row、Column）
- 弹性布局（Flex）
- 流式布局（Wrap、Flow）
- 层叠布局（Stack、Positioned）
- 对齐与相对定位（Align）

## 布局类组件简介

> 布局类组件都会包含一个或多个子组件，不同的布局类组件对子组件排版layout方式不同。
> 之前说ELement树才是最终的绘制树，Element树是通过Widget树来创建的（通过Widget.createElement()），Widget就是Element的配置数据。
> 在Flutter中，根据Widget是否包含子节点将Widget分为三类，分别对应三种Element，如下：

- LeafRenderObjectWidget
  - 对应的Element：LeafRenderObjectElement
  - 用途：Widget树的叶子节点，用于没有子节点的Widget，通常基础组件都属于这一类，如Image

- SingleChildRenderObjectWidget
  - SingleChildRenderObjectElement
  - 包含一个子Widget，如：ConstrainedBox、DecorateBox等

- MultiChildRenderObjectWidget
  - MultiChildRenderObjectElement
  - 包含多个子Widget，一般都有一个children参数，接受一个Widget数组。如Row、Column、Stack等。


> Flutter中的很多Widget是直接继承自StatelessWidget或StatefulWidget，然后再build()方法中构建真正的RenderObjectWidget。
> 如，Text，继承自StatelessWidget，然后在build()方法中通过RichText来构建其子树，而RichText才是继承自MultiChildRenderObjectWidget。所以为了方便叙述，我们也可以直接说Text属于MultiChildRenderObjectWidget（其它widget也可以这么描述），这才是本质。
> 其实StatefulWidget和StatelessWidget就是两个用于组合Widget的基类，它们本身并不关联最终的渲染对象（RenderObjectWidget）。

- 布局类组件就是指直接或间接继承（包含）MulitiChildRenderObjectWidget的Widget，一般都会有一个children属性用于接收子Widget。
- 继承关系： Widget > RenderObjectWidget > (Leaf/SingleChild/MultiChild)RenderObjectWidget 

- RenderObjectWidget类中定义了创建、更新RenderObject的方法，子类必须实现他们，关于RenderObject我们现在只需要知道它是最终布局、渲染UI界面的对象即可，也就是说，对于布局类组件来说，其布局算法都是通过对应的RenderObject对象来实现的，所以读者如果对接下来介绍的某个布局类组件的原理感兴趣，可以查看其对应的RenderObject的实现，比如Stack（层叠布局）对应的RenderObject对象就是RenderStack，而层叠布局的实现就在RenderStack中。


## 线性布局（Row、Column）

> 线性布局就是指沿水平或垂直方向排布子组件。Flutter中通过Row和Column来实现线性布局类似于ANdroid中的LinearLayout控件。
> Row和Column继承自Flex

### 主轴和纵轴

- 对于线性布局，若布局是沿水平方向，那么主轴就是指水平方向，而纵轴即垂直方向。如果布局沿垂直方向，那么主轴就是指垂直方向，而纵轴就是水平方向。

- 在线性布局中，有2个定义对齐方式的枚举类MainAxisAlignment和CrossAxisAlignment分别代表主轴对齐和纵轴对齐。

#### Row

- Row可以在水平方向排列其子widget，定义如下：

```dart
Row({
  ...  
  TextDirection textDirection,   // 表示水平方向子组件的布局顺序（从左往右还是从右往左） 
  MainAxisSize mainAxisSize = MainAxisSize.max,  //   表示Row在主轴(水平)方向占用的空间，默认是MainAxisSize.max，表示尽可能多的占用水平方向的空间，此时无论子widgets实际占用多少水平空间，Row的宽度始终等于水平方向的最大宽度；而MainAxisSize.min表示尽可能少的占用水平空间，当子组件没有占满水平剩余空间，则Row的实际宽度等于所有子组件占用的的水平空间；
  MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start, // 表示子组件在Row所占用的水平空间内对齐方式，如果mainAxisSize值为MainAxisSize.min，则此属性无意义，因为子组件的宽度等于Row的宽度。只有当mainAxisSize的值为MainAxisSize.max时，此属性才有意义，MainAxisAlignment.start表示沿textDirection的初始方向对齐，如textDirection取值为TextDirection.ltr时，则MainAxisAlignment.start表示左对齐，textDirection取值为TextDirection.rtl时表示从右对齐。而MainAxisAlignment.end和MainAxisAlignment.start正好相反；MainAxisAlignment.center表示居中对齐。读者可以这么理解：textDirection是mainAxisAlignment的参考系。
  VerticalDirection verticalDirection = VerticalDirection.down,  // 表示Row纵轴（垂直）的对齐方向，默认是VerticalDirection.down,表示从上到下。
  CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center, // 表示子组件在纵轴方向的对齐方式，Row的高度等于子组件中最高的子元素高度，它的取值和MainAxisAlignment一样(包含start、end、 center三个值)，不同的是crossAxisAlignment的参考系是verticalDirection，即verticalDirection值为VerticalDirection.down时crossAxisAlignment.start指顶部对齐，verticalDirection值为VerticalDirection.up时，crossAxisAlignment.start指底部对齐；而crossAxisAlignment.end和crossAxisAlignment.start正好相反；
  List<Widget> children = const <Widget>[], // 子组件数组
})
```

##### 示例

```dart
Column(
    // 测试Row对齐方式，排除Column默认居中对齐的干扰
    crossAxisAlignment: CrossAxisAlignment.start,
    children: <Widget>[
        Row( // 默认居中对齐
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
                Text("hello world"),
                Text("I am Jack"),
            ],
        ),
        Row( // 由于mainAxisSize的值为MainAxisSize.min,Row的宽度等于两个Text的宽度和，所以对齐没得意义，从左往右排
            mainAxisSize: MainAxisSize.min,
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
                Text("hello world"),
                Text("I am Jack"),
            ],
        ),
        Row( // Row设置textDirection值为TextDirection.rtl，所以子组件会从右向左的顺序排列，而此时MainAxisAlignment.end表示左对齐，
            mainAxisAlignment: MainAxisAlignment.end,
            textDirection: TextDirection.rtl,
            children: <Widget>[
                Text("hello world"),
                Text("I am Jack"),
            ],
        ),
        Row( // 测试纵轴的对齐方式，由于2个子Text字体不一样，所以搞得不一样，指定verticalDirection值为VerticalDirection.up，即从低向顶排列。而此时crossAxisAlignment值为CrossAxisAlignment.start表示底对齐。
            crossAxisAlignment: CrossAxisAlignment.start,
            verticalDirection: verticalDirection.up,
            children: <Widget>[
                Text("hello world", style: TextStyle(foontSize: 30.0),),
                Text("I am Jack"),
            ],
        ),
    ],
);
```

#### Column

- Column可以在垂直方向排列子组件。参数和Row一样。

- 示例：

```dart
import 'package:flutter/material.dart';

class CenterColumnRoute extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
        return Column(
            crossAxisAlignment: CrossAxisAlignment.center,
            chilren: <Widget>[
                Text("hi"),
                Text("World"),
            ]
        )
    }
}

/*
    由于没有指定Column的mainAxis,所以使用默认值MainAxisSize.max,则Column会在垂直方向占用尽可能多的空间，此例中为屏幕高度。

    由于我们指定了 crossAxisAlignment 属性为CrossAxisAlignment.center，那么子项在Column纵轴方向（此时为水平方向）会居中对齐。注意，在水平方向对齐是有边界的，总宽度为Column占用空间的实际宽度，而实际的宽度取决于子项中宽度最大的Widget。在本例中，Column有两个子Widget，而显示“world”的Text宽度最大，所以Column的实际宽度则为Text("world") 的宽度，所以居中对齐后Text("hi")会显示在Text("world")的中间部分

*/
```

### 特殊情况

- 若Row里面嵌套Row，或者Column里面再嵌套Column，那么只有最外面的Row或Column会占用尽可能大的空间，里面Row和Column所占空间为实际大小

```dart
container(
    color: Colors.green,
    child: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            mainAxisSize: MainAxisSize.max, // 有效，外层Colunm为整个屏幕
            children: <Widget>[
                Container(
                    color: Coloes.red,
                    child: Column(
                        mainAxisSize: MainAxisSize.max, // 无效，内层Column为实际高度
                        children: <Widget>[
                            Text("Hellow world"),
                            Text("I am Jack"),
                        ],
                    ),
                )
            ],
        ),
    ),
);
```

- 若要让里面的Column占满外部Column，可以使用Expanded组件：

```dart
Expanded(
    child: Container(
        color: Colors.red,
        child: Column(
            mainAxisAlignment: MainAxisAlignment.center, // 垂直方向居中对齐
            children: <Widget>[
                Text("hello world"),
                Text("I am Jack"),
            ],
        ),
    ),
)
```

## 弹性布局（Flex）

- 弹性布局允许子组件按照一定比例来分配父容器空间。
- 类似于H5的弹性盒子布局
- Flutter中的弹性布局主要通过Flex和Expanded来配合实现

### Flex

- Flex组件可以沿水平或者垂直方向排列子组件，因为Row和Column都继承自Flex，参数基本相同，所以能使用Flex的地方基本上都可以使用Row或Column。
- Flex本身功能是很强大的，它也可以和Expanded组件配合实现弹性布局。

```dart
Flex({
    ...
    @require this.direction, // 弹性布局的方向，Row默认为水平方向，Column默认为垂直方向
    List<Widget> children = const <widget>[],
    ...
})
```

- Flex继承自MultiChildRenderObjectWidget，对应的RenderObject为RenderFlex，RenderFlex中实现了其布局算法


### EXpanded

- 可以按比例“扩伸”Row,Column,Flex子组件所占的空间

```dart
const Expanded({
    int flex =1, // 弹性系数，若为0，或null，则child是没有弹性的，即不会被扩伸占用的空间。如果大于0，所有的Expanded按照其flex的比例来分割主轴的全部空闲空间。
    @required Widget child,
})
```

- 示例

```dart
class FlexLayoutTestRoute extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
        return Column(
            chilren:<widget>[
                // Flex的两个widget按1：2来占据水平空间
                Flex(
                    direction: Axis.horizontal,
                    children: <Widget>[
                        Expanded(
                            flex: 1,
                            child: Container(
                                height: 30.0,
                                color: Colors.red,
                            ),
                        ),
                        Expanded(
                            flex: 2,
                            child: Container(
                                hieght: 30.0,
                                color: Colors.green,
                            ),
                        ),
                    ],
                ),
                Padding(
                    padding: const EdgeInsets.only(top: 20.0),
                    child: SizeBox(
                        height: 100.0,
                        //Flex的三个子widget，在垂直方向按2：1：1来占用100像素的空间
                        child: Flex(
                            direction: Axis.vertical,
                            chilren: <Widget>[
                                Expanded(
                                    flex: 2,
                                    child: Container(
                                        height: 30.0,
                                        color: Colors.red,
                                    ),
                                ),
                                Spacer(
                                    flex: 1,
                                ),
                                Expanded(
                                    flex: 1,
                                    child: Container(
                                        height: 30.0,
                                        color: Colors.green,
                                    ),
                                )
                            ]
                        )
                    )
                )
            ]
        )
    }
}
```

- Spacer的功能是占用指定比例的空间，实际上它只是Expanded的一个包装类。

- 源码如下：

```dart
class Spacer extends StatelessWidget {
  const Spacer({Key key, this.flex = 1})
    : assert(flex != null),
      assert(flex > 0),
      super(key: key);

  final int flex;

  @override
  Widget build(BuildContext context) {
    return Expanded(
      flex: flex,
      child: const SizedBox.shrink(),
    );
  }
}
```


## 流式布局

- 当使用Row或Column时，若widget超出屏幕范围，则会报溢出错误

- Flutter中通过Wrap和Flow来支持流式布局

### Wrap

- 之定义：

```dart
Wrap({
  ...
  this.direction = Axis.horizontal, // 方向
  this.alignment = WrapAlignment.start, // 对齐方式
  this.spacing = 0.0, // 主轴方向子widget的间距
  this.runAlignment = WrapAlignment.start,// 纵轴方向的对齐方式
  this.runSpacing = 0.0, // 纵轴方向的间距
  this.crossAxisAlignment = WrapCrossAlignment.start,
  this.textDirection,
  this.verticalDirection = VerticalDirection.down,
  List<Widget> children = const <Widget>[],
})
```

- demo

```dart
Wrap(
    spacing: 8.0, // 主轴（水平方向间距）
    runSpacing: 4.0, // 纵轴（垂直方向间距）
    alignment: WrapAlignment.center, // 沿主轴方向居中
    children: <Widget> [
        new Chip(
        avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('A')),
        label: new Text('Hamilton'),
        ),
        new Chip(
        avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('M')),
        label: new Text('Lafayette'),
        ),
        new Chip(
        avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('H')),
        label: new Text('Mulligan'),
        ),
        new Chip(
        avatar: new CircleAvatar(backgroundColor: Colors.blue, child: Text('J')),
        label: new Text('Laurens'),
        ),
    ]
)
```

### Flow

- 很少用FLow，过于复杂



## 层叠布局Stack、Positioned

- 层叠布局和Web中的绝对定位、Android中的Frame布局是相似的，子组件可以根据距父容器四个角的位置来确定自身的位置。
- 绝对定位允许子组件堆叠起来（按照代码中声明的顺序）。
- Flutter中使用Stack和Positioned这两个组件来配合实现绝对定位。
- Stack允许子组件堆叠，而Positioned用于根据Stack的四个角来确定子组件的位置。

### Stack

```dart
Stack({
    this.alignment = AlignmentDirectional.topStart,
    this.textDirection,
    this.fit = StackFit.loose,
    this.overflow = Overflow.clip,
    List<Widget> children = const <Widget>[],
})
```


### Positioned

```dart
const Positioned({
    Key key,
    this.left,
    this.top,
    this.right,
    this.bottom,
    this.width,
    this.height,
    @required Widget child,
})
```


## 对齐与相对定位（Align）

- 通过Stack和Positioned，我们可以指定一个或多个子元素相对于父元素各个边的精确偏移，并且可以重叠。
- 但如果我们只想简单的调整一个子元素在父元素中的位置的话，使用Align组件会更简单一些。