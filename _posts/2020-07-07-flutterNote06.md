---
layout:     post
title:      Flutter学习笔记

subtitle:   第六篇
date:       2020-07-07
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - flutter
---

- 在实际开发中，当发现使用SizedBox或ConstrainedBox给子元素指定宽高，仍然没有效果，几乎可以断定父元素设置了限制。

- 示例：

```dart
AppBar(
    title: Text(title),
    actions: <Widget>[
        SizedBox(
            width: 20,
            height: 20,
            child: CircularProgressIndicator(
                strokeWidth: 3,
                valueColor: AlwaysStoppedAnimation(Colors.white70),
            ),
        )
    ],
)
```

- 因为AppBar中已经指定了action按钮的限制条件，所以我们要自定义loading按钮大小，就必须通过Unconstrained来去除父元素的限制，代码如下：

```dart
AppBar(
    title: Text(title),
    actions: <Widget>[
        unconstrainedBox(
            child: SizedBox(
                width: 20,
                height: 20,
                child: CircularProgressIndicator(
                    strokeWidth: 3,
                    valueColor: AlwaysStoppedAnimation(Colors.white70),
                )
            )
        )
    ]
)
```

## 装饰容器DecoratedBox

- DecoratedBox可以在其子组件绘制前后绘制一些装饰Decoration，如背景、边框、渐变等。定义如下：

```dart
const DecoratedBox({
    Decoration decoration, // 将要绘制的装饰，类型为Decoration
    DecorationPosition position = DecorationPosition.background, // 此属性决定在哪里绘制Decoration
    Widget child
})
```

### BoxDecoration

- 通常直接使用BoxDecoration类，是Decoration的字类，实现了常用的装饰柜hi元素的绘制

```dart
BoxDecoration({
    Color color, // 颜色
    DecorationImage image, // 图片
    BoxBorder border, // 边框
    BorderRadiusGeometry borderRadius, // 圆角
    List<BoxShadow> boxShadow, // 阴影，可以指定多个
    Gradient gradient, // 渐变
    BlendMode backgroundBlendMode, // 背景混合模式
    BoxShape shape = BoxShape.rectangle, // 形状 
})
```

- 示例

```dart
DecorateBox(
    decoration: BoxDecoration(
        gradient: LinearGradient(colors: [Colors.red, Colors.orange[700]]), // 背景渐变
        borderRadius: BorderRadius.circlular(3.0), // 3像素圆角
        boxShadow: [
            // 阴影
            BoxShadow(
                color: Colors.black54,
                offset: Offset(2.0, 2.0),
                blurRadius: 4.0
            )
        ]
    ),
    child: Padding(
        padding: EdgeInsets.symmetric(horizontal: 80.0, vertical: 18.0),
        child: Text("Login", style: TextStyle(color: Colors.white)),
    )
)
```

## 变化（Tranform）

- 可以在其子组件绘制时对其应用一些矩阵变换来实现一些特效。
- Matrix4是一个4D矩阵，通过它，可以实现各种矩阵操作

- 示例：

```dart
Container(
    color: Colors.black,
    child: new Transform(
        alignment: Alignment.topRight, // 相对于坐标系原点的对齐方式
        transform: new Matrix4.skewY(0.3), // 沿Y轴倾斜0.3弧度
        child: new Container(
            padding: const EdgeInsets.all(8.0),
            color: Colors.deepOrange,
            child: const Text('Apartment for rent!'),
        ),
    ),
);
```


### 平移

```dart
DecoratedBox(
    decoration: BoxDecoration(color: Colors.red),
    // 默认原点为左上角，左移20像素，向上平移5
    child: Transfrom.translate(
        offset: Offset(-20.0, -5.0),
        child: Text("Hello, Man"),
    )
)
```

- Transform.rotate()


## Container
- 一个组合类容器，本身不对应具体的RenderObject,
- 是DecoratedBox、ConstrainedBox、Transform、Padding、Align等组件组合的一个多功能容器，因此只需要通过一个Container组件可以实现同时需要装饰、变换、限制的场景。

- 定义：

```dart
Container({
    this.alignment,
    this.padding, // 容器内补白，属于decoration的装饰范围
    Color color, // 背景色
    Decoration decoration, // 背景装饰
    Decoration foregroundDecoration, // 前景装饰
    double width, // 容器的宽度
    double height, // 容器的高度
    BoxConstraints constraints, // 容器大小的限制条件
    this.margin // 容器的外补白。不属于decoration的装饰范围
    this.transform, //变换
    this.child,
})
```

- 示例

```dart
Container(
  margin: EdgeInsets.only(top: 50.0, left: 120.0), //容器外填充
  constraints: BoxConstraints.tightFor(width: 200.0, height: 150.0), //卡片大小
  decoration: BoxDecoration(//背景装饰
      gradient: RadialGradient( //背景径向渐变
          colors: [Colors.red, Colors.orange],
          center: Alignment.topLeft,
          radius: .98
      ),
      boxShadow: [ //卡片阴影
        BoxShadow(
            color: Colors.black54,
            offset: Offset(2.0, 2.0),
            blurRadius: 4.0
        )
      ]
  ),
  transform: Matrix4.rotationZ(.2), //卡片倾斜变换
  alignment: Alignment.center, //卡片内文字居中
  child: Text( //卡片文字
    "5.20", style: TextStyle(color: Colors.white, fontSize: 40.0),
  ),
);
```

- 容器大小可以通过width、height属性来指定，也可以通过constraints来指定；若它们同时存在，width和height优先，实际上Container内部会根据width、height来生成一个constraints
- color和decoration是互斥的，若同时设置它们则会报错。实际上，当指定color时，Container内会自动创建一个decoration


## Padding和Margin

- 二者之区别

```dart
Container(
    margin: EdgeInsets.all(20.0), // 容器外补白
    color: Colors.orange,
    child: Text("Hello world"),
),
Container(
    padding: EdgeInsets.all(20.0), // 容器内补白
    color: Colors.orange,
    child: Text("Hello World"),
),
```

- margin的留白是在容器外部，padding的留白是在容器内部
- 事实上，Container内margin和padding都是通过Padding组件来实现的，上面的代码等价于：

```dart
Padding(
    padding: EdgeInsets.all(20.0),
    child: DecoratedBox(
        decoration: BoxDecoration(color: Colors.orange),
        child: Text("Hello World"),
    ),
),
DecoratedBox(
    decoration: BoxDecoration(color: Color.orange),
    child: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Text("Hello WOrld!"),
    ),
),
```