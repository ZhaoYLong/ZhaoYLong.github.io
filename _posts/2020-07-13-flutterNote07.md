---
layout:     post
title:      Flutter学习笔记

subtitle:   第七篇
date:       2020-07-13
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - flutter
---

# Scaffold、TabBar、底部导航

## Scaffold
- 一个完整的路由页可能会包含导航栏，抽屉菜单（Drawer）以及底部Tab导航菜单。Flutter Material组件库提供了一些现成的组件来减少开发任务。
- Scaffold是一个路由页的骨架，使用之可以很容易地拼装出一个完整的页面

- 示例
  - 实现一个页面，包含：
    - 一个导航栏
    - 导航栏右边有一个奉献按钮
    - 有一个抽屉菜单
    - 有一个底部导航
    - 右下角有一个悬浮的动作按钮

- 代码：

```dart
class ScaffoldRoute extends StatefulWidget {
    @override
    _ScaffoldRouteState createState() => _ScaffoldRouteState();
}

class _ScaffoldRouteState extends State<ScaffoldRoute> {
    int _selectedIndex = 1;

    @override
    Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
                // 导航栏
                title: Text("App Name")，
                actions: <Widget>[
                    // 导航栏右侧菜单
                    IconButton(icon: Icon(Icons.share), onPressed: () {}),
                ],
            ),
            drawer: new MyDrawer(), //抽屉
            bottomNavigationBar: BottomNavigationBar(
                // 底部导航
                items: <BottomNavigationBarItem>[
                    BottomNacigationBarItem(icon: Icon(Icons.home), title: TExt("Home")),
                    BottomNavigationBarItem(icon: Icon(Icons.business), title: Text("Business")),
                    BottomNavgationBarItem(icon: Icon(Icons.school), title: Text('School')),
                ],
                currentIndex: _selectedIndex,
                fixedColor: Colors.blue,
                onTap: _onItemTapped,
            ),
            floatingActionButton: FloatingActionButton(
                // 悬浮按钮
                child: Icon(Icons.add),
                onPressed: _onAdd
            ),
        );
    }

    void _onItemTapped(int index) {
        setState(() {
            _selectedIndex = index;
        });
    }

    void _onAdd() {}
}
```

- AppBar : 一个导航栏骨架
- MyDrawer : 抽屉菜单
- BottomNavigationBar : 底部导航栏
- FloatingActionButton : 漂浮按钮


### AppBar

- 是一个Material风格的导航栏，通过它可以设置导航栏标题、导航栏菜单、导航栏底部的Tab标题等，定义：

```dart
AppBar({
    Key key,
    this.leading, // 导航栏最左侧Widget，常见为抽屉菜单按钮或返回按钮。
    this.automaticallyImplyLeading = true, // 若leading为null，是否自动实现默认的leading按钮
    this.title, // 页面标题
    this.actions, // 导航栏右侧菜单
    this.bottom, // 导航栏底部菜单，通常为Tab按钮组
    this.elevation = 4.0, //导航栏阴影
    this.centerTitle, // 标题是否居中
    this.backgroundColor, // 
    ... 
})
```

- 示例

```dart
Scaffold(
    appBar: AppBar(
        title: Text("App Name"),
        leading: Builder(builder: (context) {
            return IconButton(
                icon: Icon(Icons.dashboard. color: Colors.white), // 自定义图标
                onPressed: () {
                    // 打开抽屉菜单
                    Scaffold.of(context).openDrawer();
                },
            );
        }),
        ...
    )
)
```

- 代码中打开抽屉菜单的方法在ScaffoldState中，通过Scaffold.of(context)可以获取父级最近的Scaffold 组件的State对象。

### TabBar

- Material组件库中提供了一个TabBar组件，可以快速生成Tab菜单，下面是源码：

```dart
class _ScaffoldRouteState extends State<ScaffoldROute> width SingleTickerProviderStateMixin {
    TabController _tabController; // 需要定义一个Controller
    list tabs = ["新闻", "历史", "图片"];

    @override
    void initState() {
        super.initState();
        // 创建Controller
        _tabController = TabController(length: tabs.length, vsync: this);
    }

    @override
    Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
                ... // 省略无关代码
                bottom: TabBar(
                    controller: _tabController,
                    tabs: tabs.map((e) => Tab(text: e)).toList()
                ),
            ),
        )
    }
}
```

- Tab组件定义如下：
  
```dart
Tab({
    Key Key,
    this.text, // 菜单文本
    this.icon, // 菜单图标
    this.child, // 自定义组件样式
})
```

### TabBarView
- 通过TabBar,我们只能生成一个静态的菜单，真正的Tab页还没有实现。由于Tab菜单和Tab页的切换需要同步，我们需要通过TabController去监听Tab菜单的切换去切换Tab页，代码如：

```dart
_tabController.addListener(() {
    switch(_tabController.index) {
        case 1: ...;
        case 2: ... ;
    }
});
```

- 如果我们Tab页可以滑动切换的话，还需要在滑动过程中更新TabBar指示器的偏移！显然，要手动处理这些是很麻烦的，为此，Material库提供了一个TabBarView组件，通过它不仅可以轻松的实现Tab页，而且可以非常容易的配合TabBar来实现同步切换和滑动状态同步，示例如下：

```dart
Scaffold(
    appBar: AppBar(
        ...//省略无关代码
        bottom: TabBar(
            controller: _tabController,
            tabs: tabs.map((e) => Tab(text: e)). toList()
        ),
    ),
    drawer: new MyDrawer(),
    body: TabBarView(
        controller: _tabController,
        children: tabs.map((e) {
            // 创建3个tab页
            return Controller(
                alignment: Alignment.center,
                child: Text(e, textScaleFactor: 5),
            );
        }).toList(),
    ),
    ... // 省略无关代码
)
```

- PageView和TabView功能类似

### 抽屉菜单Drawer

- Scaffold的drawer和endDrawer属性可以分别接受一个Widget来作为页面的左右抽屉菜单。。如果开发者提供了抽屉菜单，那么当用户手指从屏幕左（或右）侧向里滑动时便可打开抽屉菜单

- 示例：

```dart
class MyDrawer extends StatelessWidget {
    const MyDrawer({
        Key key,
    }) : super(key: key);

    @override
    Widget builder(BuildContext context) {
        return Drawer(
            child: MediaQuery.removePadding(
                context: context,
                // 移除抽屉菜单顶部默认留白
                removeTop: true,
                child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: <Widget>[
                        Padding(
                            padding: const EdgeInsets.only(top: 38.0),
                            child: Row(
                                children: <Widget>[
                                    Padding(
                                        padding: const EdgeInsets.symmetric(horizontal: 16.0),
                                        child: ClipOval(
                                            child: Image.asset(
                                                "imgs/avatar.png",
                                                width: 80,
                                            ),
                                        ),
                                    ),
                                    Text(
                                        "Wendux",
                                        style: TextStyle(fontWeight: FontWeight.bold),
                                    )
                                ],
                            ),
                        ),
                        Expanded(
                            child: ListView(
                                children: <Widget>[
                                    ListTile(
                                        leading: const Icon(Icons.add),
                                        title: const Text("Add account"),
                                    ),
                                    ListTile(
                                        leading: const Icon(Icons.settings),
                                        title: const Text("Manage account"),
                                    ),
                                ],
                            ),
                        ),
                    ],
                ),
            ),
        );
    }
}
```

### FloatingActionButton

- FloatingActionButton是Material设计规范钟的一种特殊Button，通常悬浮在页面的某一个位置作为某种常用动作的快捷入口


### 底部Tab导航栏

- 通过Scaffold的bottomNavigationBar属性来设置底部导航栏

- Material组件库中提供了一个BottomAppBar 组件，它可以和FloatingActionButton配合实现这种“打洞”效果

- 源码：

```dart
bottomNavigationBar: BottomAppBar(
    color: Colors.white,
    shape: CircularNotchedRectangle(), // 底部导航栏打一个圆形的洞
    child: Row(
        children: [
            IconButton(icon: Icon(Icons.home)),
            SizedBox(), // 中间位置空出
            IconButton(icon: Icon(Icons.business)),
        ],
        mainAxisAlignment: MainAixAlignment.soaceAround, // 均分底部导航栏横向空间
    )
)
```

- 可以看到，上面代码中没有控制打洞位置的属性，实际上，打洞的位置取决于FloatingActionButton的位置，上面FloatingActionButton的位置为：

```dart
floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
```

- 所以打洞位置在底部导航栏的正中间。

- BottomAppBar的shape属性决定洞的外形，CircularNotchedRectangle实现了一个圆形的外形，我们也可以自定义外形。


# 裁剪（Clip）

- Flutter钟提供了一些裁剪函数，用于对组件进行裁剪

- Clip
  - ClipOval
  - ClipRRect
  - ClipRect

- demo

```dart
import 'package:flutter/material.dart';

class ClipTestRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 头像  
    Widget avatar = Image.asset("imgs/avatar.png", width: 60.0);
    return Center(
      child: Column(
        children: <Widget>[
          avatar, //不剪裁
          ClipOval(child: avatar), //剪裁为圆形
          ClipRRect( //剪裁为圆角矩形
            borderRadius: BorderRadius.circular(5.0),
            child: avatar,
          ), 
          Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Align(
                alignment: Alignment.topLeft,
                widthFactor: .5,//宽度设为原来宽度一半，另一半会溢出
                child: avatar,
              ),
              Text("你好世界", style: TextStyle(color: Colors.green),)
            ],
          ),
          Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              ClipRect(//将溢出部分剪裁
                child: Align(
                  alignment: Alignment.topLeft,
                  widthFactor: .5,//宽度设为原来宽度一半
                  child: avatar,
                ),
              ),
              Text("你好世界",style: TextStyle(color: Colors.green))
            ],
          ),
        ],
      ),
    );
  }
}
```

## CustomClipper
