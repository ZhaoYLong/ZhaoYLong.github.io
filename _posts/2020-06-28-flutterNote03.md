---
layout:     post
title:      Flutter学习笔记

subtitle:   第三篇
date:       2020-06-28
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - flutter
---

## Flutter SDK内置组件库介绍

- Flutter提供了一套丰富、强大的基础组件，在基础组件库之上FLutter又提供了一套Material风格（Android）和一套Cupertino（IOS）的组件库。

- 使用基础组件库

```dart
import 'package:flutter/widgets.dart';
```

- 基础组件
  - Text： 该组件可用于创建一个带格式得文本。   
  - Row、Column：这些具有弹性空间的布局类Widget可让您在水平（Row）和垂直（Column）方向上创建灵活的布局。其设计是基于Web开发中的Flexbox布局模型。
  - Stack：取代线性布局，Stack允许自widget堆叠，可以使用Positioned来定位他们相对于Stack的上下左右四条边的位置。Stacks是基于Web开发中的绝对定位布局模型设计的。
  - Container：创建矩形视觉元素。container 可以装饰一个BoxDecoration, 如 background、一个边框、或者一个阴影。 Container 也可以具有边距（margins）、填充(padding)和应用于其大小的约束(constraints)。另外， Container可以使用矩阵在三维空间中对其进行变换。

- Material组件
  - 引入组件

  ```dart
    import 'package:flutter/material.dart'';
  ```

- Cupertino组件
  - Flutter也提供了一套丰富的Cupertino风格的组件，尽管目前还没有Material 组件那么丰富，但是它仍在不断的完善中。
  - 值得一提的是在Material 组件库中有一些组件可以根据实际运行平台来切换表现风格，比如MaterialPageRoute，在路由切换时，如果是Android系统，它将会使用Android系统默认的页面切换动画(从底向上)；如果是iOS系统，它会使用iOS系统默认的页面切换动画（从右向左）。由于在前面的示例中还没有Cupertino组件的示例，下面我们实现一个简单的Cupertino组件风格的页面：

  ```dart
    // 导入cupertino widget库
    import 'package；flutter/cupertino.dart';

    class CupertinoTestRoute extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return CupertinoPageScaffold(
                navigatioBar: CupertinoNavigationBar(
                    middle: Text("cupertino Demo"),
                ),
                child: Center(
                    child: cupertinoButton(
                        color: CupertinoColors.activeBlue,
                        child: Text("Press"),
                        onPressed: () {}
                    ),
                ),
            );
        }
    }
  ```

## 状态管理

> 响应式的编程框架中都一个永恒的主题 ———— 状态(State)管理，无论是在React/Vue（两者都是支持响应式编程的Web开发框架）还是Flutter中，他们讨论的问题和解决的思想都是一致的。

- Widget管理自己的状态
- Widget管理子Widget状态
- 混合管理（父Widget和Widget都管理状态）

- 如何决定使用哪种管理方式？
  - 若状态是用户数据，如复选框的选中状态、滑块的位置，则该状态最好由父Widge管理。
  - 若状态是有关界面外观效果的，例如颜色、动画，最好由Widget本身来管理
  - 若某一个状态是不同Widget共享的，则最好在父Widget中管理（灵活更重要）


### Widget管理自身状态

- _TapboxAState类：
  - 管理TabBoxA的状态
  - 定义_active：确定盒子的当前颜色的布尔值。
  - 定义_handleTap()函数，该函数在点击该盒子时更新_active，并调用setState()更新UI
  - 实现widget的多有交互行为

```dart
// TapboxA 管理自身状态

//-------------------------TapboxA--------------

class TapboxA extends StatefulWidget {
  TapboxA({key key}) : super(key： key);

  @override
  _TapboxAState createState() => new _TapboxAState();
}

class _TapboxAState extends State<TapboxA> {
  bool _active = false;

  void _handleTap() {
    setState(() {
      _active = !_active;
    });
  }

  Widget build(BuildContext context) {
    return new GestureDetector(
      onTap: _handleTap,
      child: new Container(
        child: new Center(
          child: new Text(
            _active ? 'Active' : 'Inactive',
            style: new TextStyle(fontSize: 32.0, color: Colors.while)
          ),
        ),
        width: 200.0,
        height: 200.0,
        decoration: new BoxDecoration(
          color: _active ? Colors.lightGreen[700] : Colors.grey[600],
        ),
      ),
    );
  }
}
```

### 父Widget管理子Widget的状态

- 对于父Widget来说，管理状态并告诉其子Widget何时更新通常较好
- 在以下示例中，TapboxB通过回调将其状态导出到其父组件，状态由父组件管理，因此它的父组件为StatefulWidget。但是由于TapboxB不管理任何状态，所以TapboxB为StatelessWidget

- ParentWidgetState类：
  - 为TapboxB管理_active状态
  - 实现_handleTapboxChanged()，当盒子被点击时调用的方法
  - 当状态改变时，调用setState()更新UI

- TapboxB类：
  - 继承StatelessWidget类，因为所有的状态都由其父组件处理
  - 当检测到点击时，会通知父组件

```dart
// ParentWidget为TapboxB管理状态

// ----------------------ParentWidget-------------

class ParentWidget extends StatefulWidget {
  @override
  _ParentWidgetState createState() => new _ParentWidgetState();
}

class _ParentWidgetState extends State<ParentWidget> {
  bool _active = false;

  void _handleTapboxChanged(bool newValue) {
    setState(() {
      _active = newValue;
    });
  }
  @override
  Widget build(BuildContext context) {
    return new Container(
      child: new TapboxB(
        active: _active,
        onchange: _handleTapboxChanged,
      ),
    );
  }
}

// ----------------------------TapboxB----------------

class TapboxB extends StatelessWidget {
  TapboxB({Key key, this.active: false, @required this.onchanged}): super(key: key);

  final bool active;
  final ValueCHanged<bool> onChanged;

  void _handleTap() {
    onchanged(!active);
  }

  Widget build(BuildContext context) {
    return new GestureDetector(
      onTap: _handleTap,
      child: new Container(
        child: new Center(
          child: new Text(
            active ? 'Active' : 'Inactive',
            style: new TextStyle(fontSize: 32.0, color: Colors.white)
          ),
        ),
        width: 200.0,
        height: 200.0,
        decoration: new BoxDecoration(
          color: active ? Colors.lightGreen[700] : Colors.grey[600],
        ),
      ),
    );
  }
}
```

### 混合状态管理

- 对于一些组件来说，混合管理的方式会非常有用。在这种情况下，组件自身管理一些内部状态，而父组件管理一些其他外部状态。

- 在下面TapboxC示例中，手指按下时，盒子的周围会出现一个深绿色的边框，抬起时，边框消失。点击完成后，盒子的颜色改变。 TapboxC将其_active状态导出到其父组件中，但在内部管理其_highlight状态。这个例子有两个状态对象_ParentWidgetCState和_TapboxCState。

- ParentWidgetCState类：
  - 管理_active状态
  - 实现_handleTapboxChanged(), 当盒子被点击时调用
  - 当点击盒子并且_active状态改变时调用setState()更新UI

- _TapboxCState对象：
  - 管理_highlight状态
  - GestureDetector监听所有tap事件。当用户点下时，它添加高亮（深绿色边框）；当用户释放时，会移除高亮。
  - 当按下、抬起或者取消点击时更新_highlight状态，调用setState()更新UI
  - 当点击时，将状态的改变传递给父组件

```dart
// -------------------------ParentWidget------------

class ParentWidgetC extends StatefulWidget {
  @override
  _ParentWidgetCState createState() => new _ParentWidgetCState();
}

class _ParentWidgetCState extends State<ParentWidgetC> {
  bool _active = false;

  void _handleTapboxChanged(bool newValue) {
    setState(() {
      _active = newValue;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Container(
      child: new TapboxC(
        active: _active,
        onChanged: _handleTapboxChanged,
      ),
    );
  }
}

//-----------------TapboxC

class TapboxC extends StatefulWidget {
  TapboxC({Key key, this.active: false, @required this.onChanged})
    : super(key: key);

  final bool active;
  final ValueChanged<bool>onChanged;

  @override
  _TapboxCState createState() => new _TapboxCState();
}

class _TapboxCState extends State<TapboxC> {
  bool _hightlight = false;

  void _handleTapDown(TapDownDetails details) {
    setState(() {
      _highlight = true;
    });
  }

  void _handleTapUp(TapUpDetails details) {
    setState(() {
      _highlight = false;
    });
  }

  void _handleTapCancel() {
    setState(() {
      _highlight = false;
    });
  }

  void _handleTap() {
    widget.onChanged(!widget.active);
  }

  @overide
  Widget build(BuildContext context) {
    // 在按下时添加绿色边框，当抬起时取消高亮
    return new GestureDetector(
      onTapDown: _handleTapDown, // 处理按下事件
      onTapon: _handleTapUp, // 处理抬起事件
      onTap: _handleTap,
      onTapCancel: _handleTapCancel,
      child: new Container(
        child: new Center(
          child: new Text(
            widget.active ? 'Active' : 'Inactive',
            style: new TextStyle(fontSize: 32.0, color: Colors.white)
          ),
          width: 200.0,
          height: 200.0,
          decoration: new BoxDecoration(
            color: widget.active ? Colors.lightGreen[700] : Colors.grey[600],
            border: _highlight ? new Border.all(
              color: Colors.teal[700],
              width: 10.0,
            )
            : null,
          ),
        ),
      );
  }
}
```

### 全局状态管理

- 当应用中需要一些跨组件（包括跨路由）的状态需要同步时，上面介绍的方法便很难胜任了。比如，我们有一个设置页，里面可以设置应用的语言，我们为了让设置实时生效，我们期望在语言状态发生改变时，APP中依赖应用语言的组件能够重新build一下，但这些依赖应用语言的组件和设置页并不在一起，所以这种情况用上面的方法很难管理。这时，正确的做法是通过一个全局状态管理器来处理这种相距较远的组件之间的通信。目前主要有两种办法：

  - 实现一个全局的事件总线，将语言状态改变对应为一个事件，然后在APP中依赖应用语言的组件的initState 方法中订阅语言改变的事件。当用户在设置页切换语言后，我们发布语言改变事件，而订阅了此事件的组件就会收到通知，收到通知后调用setState(...)方法重新build一下自身即可。
  - 使用一些专门用于状态管理的包，如Provider、Redux


## 文本及样式

### Text

- Text用于显示简单样式文本，包含一些控制文本显示样式的一些属性

```dart
Text("Hello world", textAlign: TextAlign.left,);

Text("Hello world! I'm Jack."*4, maxLines:1, overflow: textOverflow.ellipsis,);

Text("Hello world",
  textScaleFactor: 1.5,
);
```

- textAlign：文本对齐方式，只有Text宽度大于文本内容长度时指定此属性才有意义。

- maxLines、overflow：指定文本显示的最大行数，默认情况下，文本是自动折行的，如果指定此参数，则文本最多不会超过指定的行。如果有多余的文本，可以通过overflow来指定截断方式，默认是直接截断，本例中指定的截断方式TextOverflow.ellipsis，它会将多余文本截断后以省略符“...”表示；

- textScaleFactor：代表文本相对于当前字体大小的缩放因子，相对于去设置文本的样式style属性的fontSize

### TextStyle

- 用于指定文本显示的样式如颜色、字体背景等

```dart
Text("Hello world",
  style: TextStyle(
    color: Colors.blue,
    fontSize: 18.0,
    height: 1.2, // 行高，但它并不是一个绝对值，而是一个因子，具体的行高等于fontSize*height
    fontFamily: "Courier",
    background: new Paint()..color=Colors.yellow,
    decoration: TextDecoration.underline,
    decorationStyle: TextDecorationStyle.dashed
  ),
);
```

- fontSize： 该属性和Text的textScaleFactor都用于控制字体大小。但是有两个主要区别：
  - fontSize可以精确指定字体大小，而textScaleFactor只能通过缩放比例来控制
  - textScaleFactor主要是用于系统字体大小设置改变时对Flutter应用字体进行全局调整，而fontSize通常用于单个文本，字体大小不会跟随系统字体大小变化。


### TextSpan 

- 代表文本的一个片段
- 定义：

```dart
const TextSpan({
  TextStyle style,
  Sting text,
  List<TextSpan> children,
  GestureRecognizer recognizer,
});
```

- style： 样式
- text： 内容
- children：是一个TextSpan的数组，也就是说TextSpan可以包括其他TextSpan
- recognizer用于对该文本片段用于手势识别处理

```dart
Text.rich(TextSpan(
  children: [
    TextSpan(
      text: "Home:"
    ),
    TextSpan(
      text: "https://flutter.dev",
      style: TextStyle(
        color: Colors.blue
      ),
      recognizer: _tapRecognizer
    ),
  ]
))
```

- 上面代码中，我们通过TextSpan实现了一个基础文本片段和一个链接片段，然后通过Text.rich 方法将TextSpan 添加到Text中，之所以可以这样做，是因为Text其实就是RichText的一个包装，而RichText是可以显示多种样式(富文本)的widget。

- _tapRecognizer: 是点击链接之后的一个处理器

### DefaultTextStyle

- 在Widget树中，文本的样式默认是可以被继承的（子类文本类组件指定具体样式时可以使用Widget树中父级设置的默认样式）

- 因此，如果在Widget树的某一个节点处设置一个默认的文本样式，那么该节点的子树中所有文本都会默认使用这个样式，而DefaultTextStyle正是用于设置默认文本样式的。

```dart
DefaultTextStyle(
  // 1.设置文本默认样式
  style: TextStyle(
    color: Colors.red,
    fontSize: 20.0,
  ),
  textAlign: TextAlign.start,
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    children: <Widget>[
      Text("hello world"),
      Text("I am Jack"),
      Text("I am Jack",
        style: TextStyle(
          inherit: false, // 2.不继承默认样式
          color: Colors.grey
        ),
      ),
    ],
  ),
);
```

### 字体

- Flutter APP可以使用不同的字体，包括第三方的字体

- 使用字体步骤：
  - 首先，在```pubspec.yaml```中声明，以确保它们会打包到应用程序中
  - 然后，通过```TextStyle```属性使用字体

#### 在asset中声明

```dart
flutter:
  fonts:
    - family: Raleway
      fonts:
        - asset: assets/fonts/Raleway-Regular.ttf
        - asset: assets/fonts/Raleway-Medium.ttf
          weight: 500
        - asset: assets/fonts/Raleway-SemiBold.ttf
          weight: 600
    - family: AbrilFatface
      fonts:
        - asset: assets/fonts/abrilfatface/AbrilFatface-Regular.ttf
```

#### 使用字体

```dart
// 声明文本样式
const textStyle = const TextStyle(
  fontFamily: 'Raleway',
);

// 使用文本样式
var buttonText = const Text(
  "Use the font for this text",
  style: textStyle,
);
```

#### Package中的字体

- 要使用Package中定义的字体，必须提供package参数

```dart
const textStyle = const TextStyle(
  fontFamily: 'Raleway',
  package: 'my_package', //指定包名
);
```

- 一个包也可以只提供字体文件而不需要在pubspec.yaml中声明。 这些文件应该存放在包的lib/文件夹中。字体文件不会自动绑定到应用程序中，应用程序可以在声明字体时有选择地使用这些字体。假设一个名为my_package的包中有一个字体文件

```dart
lib/fonts/Raleway-Medium.ttf
```

- 然后，应用程序可以声明一个字体，如下面的示例所示

```dart
 flutter:
   fonts:
     - family: Raleway
       fonts:
         - asset: assets/fonts/Raleway-Regular.ttf
         - asset: packages/my_package/fonts/Raleway-Medium.ttf
           weight: 500

// lib/是隐含的，所以它不应该包含在asset路径中
```

- 在这种情况下，由于应用程序本地定义了字体，所以在创建TextStyle时可以不指定package参数

```dart
const textStyle = const TextStyle(
  fontFamily: 'Raleway',
);
```

## 按钮

- Material组件库中提供了多种按钮组件如RaisedButton、FlatButton、OutlineButton等，都是直接或间接对RawMaterialButton组件的包装定制，故其属性都和RawMaterialButton一样。

- 所有Material 库中的按钮都有如下相同点：
  - 按下时都会有"水波动画"
  - 有一个onPressed属性来设置点击回调，当按钮按下会执行该回调，若不提供该回调则按钮会处于禁用状态，禁用状态不响应用户点击


### RaisedButton

- RaisedButton 即"漂浮"按钮，它默认带有阴影和灰色背景。按下后，阴影会变大

```dart
RaisedButton(
  child: Text("normal"),
  onPressed: () {},
);
```

### FlatButton

- FlatButton即扁平按钮，默认背景透明并不带阴影。按下后，会有背景色

```dart
FlatButton(
  child: Text("normal"),
  onPressed: () {},
)
```

### OutlineButton

- OutlineButton默认有一个边框，不带阴影且背景通明。按下后，边框样式会变亮、同时出现背景和阴影（较弱）

```dart
OutlineButton(
  child: Text("normal");
  onPressed: () {},
)
```

### IconButton

- IconButton是一个可点击的Icon，不包括文字，默认没有背景，点击后会出现背景

```dart
IconButton(
  icon: Icon(Icons.thumb_up),
  onPressed: () {},
)
```

### 带图标的按钮

- RaiseButton、FlatButton、OutlineButton都有一个icon构造函数，通过之可以轻松创建带图标的按钮

```dart
RaiseButton.icon(
  icon: Icon(Icons.send),
  label: Text(""发送),
  onPressed: _onPressed,
),
OutlineButton.icon(
  icon: Icon(Icons.add),
  label: Text("添加"),
  onPressed: _onPressed,
),
FlatButton.icon(
  icon: Icon(Icons.info),
  label: Text("详情"),
  onPressed: _onPressed,
),
```

### 自定义按钮外观

- 可以通过其属性来定义，不同按钮属性大同小异。

```dart
const FlatButton({
  ...
  @required this.onPressed, // 按钮点击回调
  this.textColor, // 按钮文字颜色
  this.disabledTextColor, // 按钮禁用时的文字颜色
  this.color, // 按钮背景颜色
  this.disabledColor, // 按钮禁用时的背景颜色
  this.hightlightColor, // 按钮按下时的背景颜色
  this.splashColor, // 点击时，水波动画中水波的颜色
  this.colorBrightness, // 按钮主题，默认浅色
  this.padding, // 按钮的填充
  this.shape, //外形
  @required this.child, //按钮的内容
})
```

- 定义一个背景蓝色，两边圆角的按钮

```dart
FlatButton(
  color: Colors.blue,
  hightlightColor: Colors.blue[700],
  colorBrightness: Brightness.dark,
  splashColor: COlors.gery,
  child: Text("Submit"),
  shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(20.0)),
  onPressed: () {},
)
```

## 图片及ICON

### 图片

- Flutter中，可以通过Image组件来加载并显示图片
- Image的数据源可以是asset、文件、内存及忘了。

### ImageProvide
- 是一个抽象类，主要定义了图片数据获取的接口load()，从不同的数据源获取图片需要实现不同的ImageProvider ，如AssetImage是实现了从Asset中加载图片的ImageProvider，而NetworkImage实现了从网络加载图片的ImageProvider。

### Image

- Image Widget有一个必选的image参数，它对应一个ImageProvider.

### 从asset中加载图片

- 1. 在工程根目录下创建一个images目录,并将图片avatar.png拷贝到该目录.
- 2. 在pubspec.yaml中的flutter部分添加如下命令：

```dart
  assets:
    - images/avatar.png
```

> 注意: 由于 yaml 文件对缩进严格，所以必须严格按照每一层两个空格的方式进行缩进，此处assets前面应有两个空格。

- 加载该图片

```dart
Image(
  image: AssetImage("image/avatar.png"),
  width: 100.0
);
```

- Image也提供了一个快捷的构造函数Image.asset用于从asset中加载、显示图片：

```dart
Image.asset("images/avatar.png",
  width: 100.0,
)
```

### 从网络加载图片

```dart
Image(
  image: NetworkImage(
    "https://avatars2.githubusercontent.com/u/20411648?s=460&v=4"
  ),
  width: 100.0,
)
```

- Image也提供了一个快捷的构造函数Image.network用于从网络加载、显示图片

```dart
Image.network(
  "https://avatars2.githubusercontent.com/u/20411648?s=460&v=4",
  width: 100.0,
)
```

### 参数

- Image在显示图片时定义了一系列参数，通过这些参数可以控制图片的显示外观、大小、混合效果等。

```dart
const Image({
  ...
  this.width,
  this.height,
  this.color, // 图片的混合模式
  this.colorBlendMode, // 混合模式
  this.fit, // 缩放模式
  this.alignment = Alignment.cneter, // 对齐方式
  this.repeat = ImageRepeat.noRepeat, // 重复方式
  ...
})
```

- width、height: 用于设置图片的宽、高，当不指定宽高时，图片会根据当前父容器的限制，尽可能的显示其原始大小，如果只设置width、height的其中一个，那么另一个属性默认会按比例缩放，但可以通过下面介绍的fit属性来指定适应规则。

- fit: 该属性用于在图片的显示空间和图片本身大小不同时指定图片的适应模式。适应模式是在BoxFit中定义，它是一个枚举类型，有如下值：

  - fill: 会拉伸充满显示空间，图片本身长宽比会发生变化，图片会变形。
  - 会按图片的长宽比放大后居中填满显示空间，图片不会变形，超出显示空间部分会被剪裁。
  - contain：这是图片的默认适应规则，图片会在保证图片本身长宽比不变的情况下缩放以适应当前显示空间，图片不会变形.
  - fitWidth：图片的宽度会缩放到显示空间的宽度，高度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁.
  - none：图片没有适应策略，会在显示空间内显示图片，如果图片比显示空间大，则显示空间只会显示图片中间部分

- color和colorBlendMode：在图片绘制时，可以对每一个像素进行颜色混合处理，color指定混合色，而colorBlendMode指定混合模式，

```dart
Image(
  image: AssetImage("images/avatar.png"),
  width: 100.0,
  color: Colors.blue,
  colorBlendMode: BlendMode.differencef,
);
```

- repeat: 当图片大小小于显示空间时，指定图片的重复规则

```dart
Image(
  image: AssetImage("images/avatar.png"),
  width: 100.0,
  height: 200.0,
  repeat: ImageRepeat.repeatY,
)
```

### Image缓存

- Flutter框架对加载过的图片是有缓存的（内存），默认最大缓存数量是1000，最大缓存空间为100M。

### Icon

- Flutter中，可以像Web开发一样使用iconfont，iconfont即“字体图标”，它是将图标做成字体文件，然后通过指定不同的字符而显示不同的图片

> 在字体文件中，每一个字符都对应一个位码，而每一个位码对应一个显示字形，不同的字体就是指字形不同，即字符对应的字形是不同的。而在iconfont中，只是将位码对应的字形做成了图标，所以不同的字符最终就会渲染成不同的图标。

- 在Flutter开发中，iconfont和图片相比之优势：
  - 体积小：可以减少安装包大小
  - 矢量的：iconfont都是矢量图标
  - 可以应用文本样式：可以像文本一样改变字体的颜色、大小
  - 可以通过TextSpan和文本混用

#### Material Design字体图标

- Flutter默认包含了一套Material Design的字体图标，在pubspec.yaml文件中的配置如下：

```dart
flutter:
  uses-material-design: true
```

- 简单例子：

```dart
String icons = "";

icons += "\uE914";
// error: &#xE000; or 0xE000 or E000
icons += " \uE000";
// fingerprint: &#xE90D; or 0xE90D or E90D
icons += " \uE90D";

Text(icons,
  style: TextStyle(
    fontFamily: "MaterialIcons",
    fontSize: 24.0,
    color: Colors.green
  ),
);
```

- 通过这个示例可以看到，使用图标就像使用文本一样，但是这种方式需要我们提供每个图标的码点，这并对开发者不友好，所以，Flutter封装了IconData和Icon来专门显示字体图标，上面的例子也可以用如下方式实现

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.center,
  children: <Widget>[
    Icon(Icons.accessible,color: Colors.green,),
    Icon(Icons.error,color: Colors.green,),
    Icon(Icons.fingerprint,color: Colors.green,),
  ],
)
```

### 使用自定义字体图标


## 单选开关和复选框

- Material组件提供Material风格的单选开关Switch 和 复选框 checkbox
- 它们都是继承自StatefulWidget，但它们本身不会保存当前选中状态，选中状态都是由父组件来管理的.
- 回调函数onChanged()

```dart
class SwitchAndCheckBoxTestRoute extends StatefulWidget {
  @override
  _SwitchAndCheckBoxTestRouteState createState() => _SwitchAndCheckBoxTestRouteState();
}

class _SwitchAndCheckBoxTestRouteState extends State<SwitchAndCheckBoxTestRoute> {
  bool _switchSelected = true; // 维护单选状态
  bool _checkboxSelected = true; // 维护复选框状态

  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        Switch(
          value: _switchSelected, // 当前状态
          onChanged: (value){
            // 重构页面
            setState(() {
              _switchSelected = value;
            });
          },
        ),
        Checkbox(
          value: _checkboxSelected,
          activeColor: Colors.red, // 选中时的颜色
          onChanged: (value){
            setState(() {
              _checkboxSelected = value;
            });
          },
        )
      ],
    );
  } 
}
```

### 属性外观

- Switch和Checkbox属性比较简单，读者可以查看API文档，它们都有一个activeColor属性，用于设置激活态的颜色。至于大小，到目前为止，Checkbox的大小是固定的，无法自定义，而Switch只能定义宽度，高度也是固定的。值得一提的是Checkbox有一个属性tristate ，表示是否为三态，其默认值为false ，这时Checkbox有两种状态即“选中”和“不选中”，对应的value值为true和false 。如果tristate值为true时，value的值会增加一个状态null，读者可以自行了解。

