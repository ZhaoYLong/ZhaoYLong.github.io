---
layout:     post
title:      Flutter学习笔记

subtitle:   第四篇
date:       2020-07-01
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - flutter
---

## 输入框及表单

- Material组件库提供了输入框组件TextField和表单组件Form。

###  TextField

- 用于文本输入

- 属性：

```dart
const TextField({
  ...
  TextEditingController controller, // 编辑框的控制器，通过它可以设置/获取编辑框的内容、选择编辑内容、监听编辑文本改变事件，需要显示提供一个controller来与文本框交互，如果没有，TextField内部会自动创建一个。
  FocusNode focusNode, // 用于控制TextField是否占有当前键盘的输入焦点。是和键盘交互的一个句柄。
  InputDecoration decoration = const InputDecoration(), // 用于控制Text的外观显示，如提示文本、背景颜色、边框等
  TextInputType keyboardType, // 用于设置该输入框默认的键盘输入类型
    // TextInputType枚举值：
    /*
        text : 文本输入键盘
        multiline ： 多行文本，需要和maxLines配合使用（设为null或大于1）
        number ： 数字；会弹出数字键盘
        phone :  
        datetime :
        emailAddress :
        url : 
    */
  TextInputAction textInputAction, // 键盘动作按钮图标（回车键位图标）
  TextStyle style, // 正在编辑的文本样式
  TextAlign textAlign = TextAlign.start, // 输入框内编辑文本在水平方向的对齐方式
  bool autofocus = false, // 是否自动获取焦点
  bool obscureText = false, // 是否隐藏正在编辑的文本，如密码输入
  int maxLines = 1, // 输入框的最大行数， 默认为1， 为null则无行数限制
  int maxLength, // 最大长度
  bool maxLengthEnforced = true,//maxLengthEnforced决定当输入文本长度超过maxLength时是否阻止输入，为true时会阻止输入，为false时不会阻止输入但输入框会变红。
  ValueChanged<String> onChanged, // 输入框内容改变时的回调函数；内容改变事件也可以通过controller来监听
  VoidCallback onEditingComplete, // 
  ValueChanged<String> onSubmitted, //
  /*
    这两个回调都是在输入框输入完成时触发，比如按了键盘的完成键（对号图标）或搜索键（🔍图标）。不同的是两个回调签名不同，onSubmitted回调是ValueChanged<String>类型，它接收当前输入内容做为参数，而onEditingComplete不接收参数。
  */
  List<TextInputFormatter> inputFormatters, //用于指定输入格式；当用户输入内容改变时，会根据指定的格式来校验。
  bool enabled, // false : 输入框会被禁用，
  this.cursorWidth = 2.0,
  this.cursorRadius,
  this.cursorColor,
  /*这三个属性是用于自定义输入框光标宽度、圆角和颜色的*/
  ...
})
```

- 示例： 登录输入框

  - 布局

  ```dart
    Column(
        children: <Widget>[
            TextField(
                autofocus: true,
                decoration: InputDecoration(
                    labelText: '用户名',
                    hinText: '用户名或邮箱', // 类似html里的回显
                    prefixIcon: Icon(Icons.person)
                ),
            ),
            TextField(
                decoration: InputDecoration(
                    labelText: '密码',
                    hinText: '您的登录密码',
                    prefixIcon: Icon(Icons.lock)
                ),
            ),
        ],
    );
  ```

- 获取输入内容
  - 1.定义2个变量，用于保存用户名和密码，然后再onChange触发时，各自保存一下输入内容
  - 2.通过controller直接获取

- 以第二个举例：
  
    ```dart
        //定义一个controller
        TextEditingController _ unameController = TextEditingController();

        // 设置输入框controller
        TextField(
            autofocus: true,
            controller: _unameController. // 设置controller
            ...
        )

        // 通过controller获取输入框内容
        print(_unameController.text)
    ```

- 监听文本变化
  - 设置onChange回调
  - 通过controller监听

  ```dart
  TextField(
      autofocus: true,
      onChanged: (v) {
          print("onChange: $v");
      }
  )

  // 通过controller监听
  @override
  void initState() {
      // 监听输入改变
      _unameController.addListener(() {
          print(_unameController.text);
      });
  }
  ```

- 控制焦点
  - 焦点可以通过FocusNode和FocusScopeNode来控制，默认情况下，焦点由FocusScope来管理，它代表焦点控制范围，可以在这个范围内可以通过FocusScopeNode在输入框之间移动焦点、设置默认焦点等

- 示例

```dart
class FocusTestRoute extends StatefulWidget {
  @override
  _FocusTestRouteState createState() => new _FocusTestRouteState();
}

class _FocusTestRouteState extends State<FocusTestRoute> {
  FocusNode focusNode1 = new FocusNode();
  FocusNode focusNode2 = new FocusNode();
  FocusScopeNode focusScopeNode;

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: EdgeInsets.all(16.0),
      child: Column(
        children: <Widget>[
          TextField(
            autofocus: true, 
            focusNode: focusNode1,//关联focusNode1
            decoration: InputDecoration(
                labelText: "input1"
            ),
          ),
          TextField(
            focusNode: focusNode2,//关联focusNode2
            decoration: InputDecoration(
                labelText: "input2"
            ),
          ),
          Builder(builder: (ctx) {
            return Column(
              children: <Widget>[
                RaisedButton(
                  child: Text("移动焦点"),
                  onPressed: () {
                    //将焦点从第一个TextField移到第二个TextField
                    // 这是一种写法 FocusScope.of(context).requestFocus(focusNode2);
                    // 这是第二种写法
                    if(null == focusScopeNode){
                      focusScopeNode = FocusScope.of(context);
                    }
                    focusScopeNode.requestFocus(focusNode2);
                  },
                ),
                RaisedButton(
                  child: Text("隐藏键盘"),
                  onPressed: () {
                    // 当所有编辑框都失去焦点时键盘就会收起  
                    focusNode1.unfocus();
                    focusNode2.unfocus();
                  },
                ),
              ],
            );
          },
          ),
        ],
      ),
    );
  }

}
```

- 监听焦点状态改变事件
  - FocusNode继承自ChangeNotifier,通过FocusNode可以监听焦点的改变事件

```dart
...
// 创建 focusNode   
FocusNode focusNode = new FocusNode();
...
// focusNode绑定输入框   
TextField(focusNode: focusNode);
...
// 监听焦点变化    
focusNode.addListener((){
   print(focusNode.hasFocus);
});

```

- 自定义样式

```dart
TextField(
    decoration: InputDecoration(
        labelText: "请输入用户名",
        prefixIcon: Icon(Icons.person),
        // 未获得焦点下划线为灰色
        enabledBorder: UnderlineInputBorder(
            borderSide: BorderSide(color: Colors.grey),
        ),
        // 获得焦点下划线设为蓝色
        focusedBorder: UnderlineInputBorder(
            borderSide: BorderSide(color: Colors.blue),
        ),
    ),
),
```


## 表单Form

- 对输入框进行分组，然后统一操作，如输入内容校验、输入框重置、保存等。

- Form继承自StatefulWidget对象，它对应的状态类为FormState。我们先看看Form类的定义：

```dart
Form({
    @required Widget child,
    bool autovalidate = false, // 是否自动校验输入内容
    willPopCallback onWillPop, // 决定Form所在的路由是否可以直接返回（如点击返回按钮），该回调返回一个Future对象，如果Future的最终结果是false，则当前路由不会返回；如果为true，则会返回到上一个路由。此属性通常用于拦截返回按钮
    VoidCallback onChanged, // Form的任意一个子FormField内容发生变化时会触发此回调
})
```

### FormField

- Form的子孙元素是FormField类型，FormField是一个抽象类，定义几个属性，FormState内部通过它们来完成操作，FormField部分定义如下：

```dart
const FormField({
    ...
    FormFieldSetter<T> onSaved, // 保存回调
    FormFieldValidator<T> validator, // 验证回调
    T initialValue, // 初始值
    bool autovalidate = false , // 是否自动校验
})
```

- 为了方便使用，Flutter提供了一个TextFormField组件，它继承自FormField类，也是TextField的一个包装类，所以除了FormField定义的属性之外，它还包括TextField的属性

### FormState

- FormState.validate()
- FormState.save()
- FormState.reset()

### 示例

```dart
class FormTestRoute extends StatefulWidget {
    @override
    _FormTestRouteState createState() => new _FormTestRouteState();
}

class _FormTestRouteState extends State<FormTestRoute> {
    TextEditingController _unameController = new TextEditingController();
    TextEditingController _pwdController = new TextEditingController();
    GlobalKey _formKey = new GlobalKey<FormState>();

    @override
    Widget build(BuildCOntext context) {
        return Scaffold(
            appBar: AppBar(
                title: Text("Form Test"),
            ),
            body: Padding(
                padding: const EdgeInsets.symmetric(vertical: 16.0, horizontal: 24.0),
                child: Form(
                    key: _formKey, // 设置globalKey,用于后面获取FormState
                    autovalidate: true, // 开启自动校验
                    child: <Widget>[
                        TextFormField(
                            autoFocus: true,
                            controller: _unameController,
                            decoration: InputDecoration(
                                labelText: "用户名",
                                hintText: "用户名或邮箱",
                                icon: Icon(Icons.person)
                            ),
                            // 校验用户名
                            validator: (v) {
                                return v.trim().length > 0 ? null : '用户名不能为空';
                            }
                        ),
                        TextFromField(
                            controller: _pwdController,
                            decoration: InputDecoration(
                                labelText: '密码',
                                hintTxt: '您的登录密码',
                                icon: Icon(Icons.lock)
                            ),
                            obscureText: true,
                            // 校验密码
                            validator: (v) {
                                v
                                .trim()
                                .length > 5 ? null : '密码不能少于6位'
                            }
                        )，

                        // 登录按钮
                        Padding(
                            padding: const EdgeInsets.only(top: 28.0),
                            child: Row(
                                children: <Widget>[
                                    Expanded(
                                        child: RaiseButton(
                                            padding: EdgeInsets.all(15.0),
                                            child: Text("登录"),
                                            color: Theme.of(context).primaryColor,
                                            textColor: Color.white,
                                            onPressed: () {
                                                //在这里不能通过此方式获取FormState，context不对
                                                //print(Form.of(context));

                                                // 通过_formKey.currentState 获取FormState后，
                                                // 调用validate()方法校验用户名密码是否合法，校验
                                                // 通过后再提交数据。
                                                if((_formKey.currentState as FormState).validate()){
                                                    //验证通过提交的数据
                                                }
                                            },
                                        ),
                                    ),
                                ],
                            ),
                        )
                    ],
                ),
            ),
        );
    }
}
```

- 注意，登录按钮的onPressed方法中不能通过Form.of(context)来获取，原因是，此处的context为FormTestRoute的context，而Form.of(context)是根据所指定context向根去查找，而FormState是在FormTestRoute的子树中，所以不行。正确的做法是通过Builder来构建登录按钮，Builder会将widget节点的context作为回调参数：

```dart
Expanded(
    // 通过Builder来获取RaisedButton所在widget树的真正context(Element)
    child: Builder(builder: (context){
        return RaiseButton(
            ...
            onPressed: () {
                //由于本widget也是Form的子代widget，所以可以通过下面方式获取FormState  
            if(Form.of(context).validate()){
            //验证通过提交数据
            }
        )
    })
)
```

## 进度指示器

- Material组件库提供两种进度指示
  - LinearProgressIndicator : 精确的进度指示， 用于任务进度可以计算和预估的情况
  - CircularProgressIndicator ：模糊的进度指示，用于用户任务进度无法准确获得的情况，下拉刷新等

### LinearProgressIndicator

- 是一个线性、条状的进度条，定义如下：

```dart
LinearProgressIndicator({
    double value, // 表示当前进度，取值范围[0,1];若value=null时，则指示器会执行一个循环动画（模糊进度）；当value不为null时，指示器为一个具体进度的进度条
    Color backgroundColor, // 指示器的背景色
    Animation<Color> valueColor, // 指示器的进度条样式
})
```

- 示例

```dart
// 模糊进度条（会执行一个动画）
LinearProgressIndicator(
    backgroundColor: Colors.grey[200],
    valueColor: AlwaysStoppedAnimation(Colors.blue),
),

//进度条显示50%
LinearProgressIndicator(
  backgroundColor: Colors.grey[200],
  valueColor: AlwaysStoppedAnimation(Colors.blue),
  value: .5, 
)
```

- 第一个进度条在执行循环动画：蓝色条一直在移动，而第二个进度条是静止的，停在50%的位置

### CircularProgressIndictor

- 圆形进度条

```dart
circularProgressIndicator({
  double value,
  Color backgroundColor,
  Animation<Color> valueColor,
  this.strokeWidth = 4.0, // 圆形进度条的粗细
})
```

- 示例

```dart
// 模糊进度条(会执行一个旋转动画)
CircularProgressIndicator(
  backgroundColor: Colors.grey[200],
  valueColor: AlwaysStoppedAnimation(Colors.blue),
),
//进度条显示50%，会显示一个半圆
CircularProgressIndicator(
  backgroundColor: Colors.grey[200],
  valueColor: AlwaysStoppedAnimation(Colors.blue),
  value: .5,
),
```

### 自定义尺寸

- 二者都是取父容器的尺寸作为绘制的边界，知道这个我们就可以通过尺寸限制类Widget

```dart
// 线性进度高度指定为3
SizeBox(
    height: 3,
    child: LinearProgressIndicator(
        backgroundColor: Colors.grey[200],
        valueColor: AlwaysStopAnimation(Colors.blue),
        value: .5,
    ),
),

// 圆形进度条直径指定为100
SizedBox(
  height: 100,
  width: 100,
  child: CircularProgressIndicator(
    backgroundColor: Colors.grey[200],
    valueColor: AlwaysStoppedAnimation(Colors.blue),
    value: .7,
  ),
),
```

- 若圆形进度条的父容器的高和宽不一致，则会显示成椭圆。

### 进度色动画

- valueColor

```dart
import 'package:flutter/material.dart';

class ProgressRoute extends StatefulWidget {
    @override
     _ProgressRouteState createState() => _ProgressRouteState();
}

class _ProgressRouteState extends State<ProgressRoute> with SingleTickerProviderStateMixin {
    AnimationController _animationController;

    @override
    void initState() {
        // 动画执行时间3秒
        _animationController = new AnimationController(vsync: this, duration: Duration(seconds: 3));
        _animationController.forward();
        _animationController.addListener(() => setState(() => {}));
        super.initState();
    }

    @override
    void dispose() {
        _animationController.dispose();
        super.dispose();
    }

    @override
    Widget build(BuildContext context) {
        return SingleChildScrollView(
            child: Column(
                children: <Widget>[
                    Padding(
                    padding: EdgeInsets.all(16),
                    child: LinearProgressIndicator(
                    backgroundColor: Colors.grey[200],
                    valueColor: ColorTween(begin: Colors.grey, end: Colors.blue)
                        .animate(_animationController), // 从灰色变成蓝色
                    value: _animationController.value,
                    ),
                );
                ],
            ),
            );
    }
}
```