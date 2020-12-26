---
layout:     post
title:      Flutter学习笔记

subtitle:   第八篇 —— 实战篇
date:       2020-07-14
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - flutter
---

> 本例来源于《Flutter实战》的第15章

- 实现一个简单的Github客户端，主要目标有2个：
  - 了解如何使用Flutter来开发一个完整APP，了解Flutter APP开发流程及工程结构等
  - 对前面所学内容的一个应用和总结


- 只实现一个App的骨架，实现如下功能：
  - 实现GitHub账号登录、退出登录功能
  - 登录后查看个人的项目主页
  - 支持多语言
  - 支持换肤
  - 登录状态可以持久化

- 实现上面这些功能涉及到的技术点
  - 网络请求；需要请求Github API
  - JSON 转 Dart Model
  - 全局状态管理；语言、主题、登录状态等需要全局共享
  - 持久化存储；保存登录信息，用户信息等
  - 支持国际化。intl包的使用


## Flutter APP代码结构

```dart
- github_client_app
  - android
  - ios
  - lib // dart代码位置
  - test
  - imgs // 保存图片
  - fonts // 保存icon文件
  - jsons // 在根目录下再创建一个用于保存Json文件的“jsons”文件夹
  - l10n-arb //保存各国语言对应的arb文件

- lib
  - common // 工具类，通用的方法类、网络接口类等
  - l10n // 国际化相关的类
  - models // Json文件对应的Dart Model类
  - states // 保存APP中需要跨组件共享的状态类
  - routes // 存放所有路由页面类
  - widgets // APP内封装的一些Widget组件

```

## Model类定义

- 先梳理一下APP中将用到的数据，然后生成相对应的Dart Model类。
- Json文件转Dart Model的方案采用前面介绍过的 json_model 包方案

### Github账号信息
- Github API接口返回JSON结构如下：

```dart
{
  "login": "octocat", //用户登录名
  "avatar_url": "https://github.com/images/error/octocat_happy.gif", //用户头像地址
  "type": "User", //用户类型，可能是组织
  "name": "monalisa octocat", //用户名字
  "company": "GitHub", //公司
  "blog": "https://github.com/blog", //博客地址
  "location": "San Francisco", // 用户所处地理位置
  "email": "octocat@github.com", // 邮箱
  "hireable": false,
  "bio": "There once was...", // 用户简介
  "public_repos": 2, // 公开项目数
  "followers": 20, //关注该用户的人数
  "following": 0, // 该用户关注的人数
  "created_at": "2008-01-14T04:33:35Z", // 账号创建时间
  "updated_at": "2008-01-14T04:33:35Z", // 账号信息更新时间
  "total_private_repos": 100, //该用户总的私有项目数(包括参与的其它组织的私有项目)
  "owned_private_repos": 100 //该用户自己的私有项目数
  ... //省略其它字段
}
```
- 在jsons目录下创建一个“user.json”文件保存上述信息

### API缓存策略信息
- 由于Github服务器在国内访问速度较慢，我们对Github API应用一些简单的缓存策略。我们在“jsons”目录下创建一个“cacheConfig.json”文件缓存策略信息，定义如下：

```dart
"enable": true, // 是否启用缓存
"maxAge": 1000, // 缓存的最长时间，单位（秒）
"maxCount": 100, // 最大缓存数
```

### 用户信息

- 用户信息（Profile）应包括如下信息：
  - 1、 Github账号信息；由于我们的APP可以切换账号登录，且登录后再次打开则不需要登录，所以我们需要对用户账号信息和登录状态进行持久化。
  - 2、应用使用配置信息；每一个用户都应有自己APP配置信息，如主题、语言、以及数据缓存策略等
  - 3、用户注销登录后，为了方便用户在退出APP前再次登录，我们需要记住上次登录的用户名

- 目前Github有三种登录方式，分别是账号密码登录、oauth授权登录、二次认证登录；这三种登录方式的安全性依次加强，但是在本示例中，为了简单起见，我们使用账号密码登录，因此我们需要保存用户的密码。

- 在jsons目录下创建一个profile.json文件，结构如下：
  
```dart
{
  "user":"$user", //Github账号信息，结构见"user.json"
  "token":"", // 登录用户的token(oauth)或密码
  "theme":5678, //主题色值
  "cache":"$cacheConfig", // 缓存策略信息，结构见"cacheConfig.json"
  "lastLogin":"", //最近一次的注销登录的用户名
  "locale":"" // APP语言信息
}
```

### 项目信息
- 由于APP主页要显示其所有项目信息，我们在“jsons”目录下创建一个“repo.json”文件保存项目信息。通过参考Github 获取项目信息的API文档，定义出最终的“repo.json”文件结构，如下：

```dart
{
  "id": 1296269,
  "name": "Hello-World", //项目名称
  "full_name": "octocat/Hello-World", //项目完整名称
  "owner": "$user", // 项目拥有者，结构见"user.json"
  "parent":"$repo", // 如果是fork的项目，则此字段表示fork的父项目信息
  "private": false, // 是否私有项目
  "description": "This your first repo!", //项目描述
  "fork": false, // 该项目是否为fork的项目
  "language": "JavaScript",//该项目的主要编程语言
  "forks_count": 9, // fork了该项目的数量
  "stargazers_count": 80, //该项目的star数量
  "size": 108, // 项目占用的存储大小
  "default_branch": "master", //项目的默认分支
  "open_issues_count": 2, //该项目当前打开的issue数量
  "pushed_at": "2011-01-26T19:06:43Z",
  "created_at": "2011-01-26T19:01:12Z",
  "updated_at": "2011-01-26T19:14:43Z",
  "subscribers_count": 42, //订阅（关注）该项目的人数
  "license": { // 该项目的开源许可证
    "key": "mit",
    "name": "MIT License",
    "spdx_id": "MIT",
    "url": "https://api.github.com/licenses/mit",
    "node_id": "MDc6TGljZW5zZW1pdA=="
  }
  ...//省略其它字段
}
```

### 生成Dart Model类

- 需要的Json数据已经定义完毕，现在只需要 运行json_model package提供的命令来通过json文件生成相应的Dart类：

```dart
flutter package pub run json_model
```

- 命令执行成功后，可以看到lib/models文件夹下会生成相应的Dart Model类

```dart
├── models
│   ├── cacheConfig.dart
│   ├── cacheConfig.g.dart
│   ├── index.dart
│   ├── profile.dart
│   ├── profile.g.dart
│   ├── repo.dart
│   ├── repo.g.dart
│   ├── user.dart
│   └── user.g.dart

```

### 数据持久化
我们使用shared_preferences包来对登录用户的Profile信息进行持久化。shared_preferences是一个Flutter插件，它通过Android和iOS平台提供的机制来实现数据持久化。由于shared_preferences的使用非常简单，读者可以自行查看其文档，在此不再赘述。

## 全局变量及共享状态

- 应用程序中通常会包含一些贯穿APP生命周期的变量信息，这些信息在APP大多数地方可能都会被用到，比如当前用户信息、Local信息等。
- 需要全局共享的信息分为两类： 
  - 全局变量：单纯指会贯穿整个APP生命周期的变量，用于单纯的保存一些信息或封装全局工具和方法的对象
  - 共享状态：发生改变时需要通知所有使用该状态的组件，而后者不需要。
- 为此，我们将全局变量和共享状态分开单独管理

### 全局变量-Global类

- 在lib/common目录下创建一个Global类，主要管理APP的全局变量，定义如下：

```dart
// 提供五套可选主题色
const _themes = <MaterialColor>[
  Colors.blue,
  Colors.cyan,
  Colors.teal,
  Colors.green,
  Colors.red,
];

class Global {
  static SharedPreferences _prefs;
  static Profile profile = Profile();
  // 网络缓存对象
  static NetCache netCache = NetCache();

  // 可选的主题列表
  static List<MaterialColor> get themes => _themes;

  // 是否为release版
  static bool get isRelease => bool.fromEnvironment("dart.vm.product");

  //初始化全局信息，会在APP启动时执行
  static Future init() async {
    _prefs = await SharedPreferences.getInstance();
    var _profile = _prefs.getString("profile");
    if (_profile != null) {
      try {
        profile = Profile.fromJson(jsonDecode(_profile));
      } catch (e) {
        print(e);
      }
    }

    // 如果没有缓存策略，设置默认缓存策略
    profile.cache = profile.cache ?? CacheConfig()
      ..enable = true
      ..maxAge = 3600
      ..maxCount = 100;

    //初始化网络请求相关配置
    Git.init();
  }

  // 持久化Profile信息
  static saveProfile() =>
      _prefs.setString("profile", jsonEncode(profile.toJson()));
}
```

- 需要注意的是init()需要在APP启动时，就要执行，所以应用的main()如下：

```dart
void main() => Global.init().then((e) => runApp(MyApp()));
```

- 此，一定要确保Global.init()方法不能抛出异常，否则 runApp(MyApp())根本执行不到。

### 共享状态

- 有了全局变量，我们还需要考虑如何跨组件共享状态。当然，如果我们将要共享的状态全部用全局变量替代也是可以的，但是这在Flutter开发中并不是一个好主意，因为组件的状态是和UI相关，而在状态改变时我们会期望依赖该状态的UI组件会自动更新，如果使用全局变量，那么我们必须得去手动处理状态变动通知、接收机制以及变量和组件依赖关系。因此，本实例中，我们使用前面介绍过的Provider包来实现跨组件状态共享，因此我们需要定义相关的Provider。在本实例中，需要共享的状态有登录用户信息、APP主题信息、APP语言信息。由于这些信息改变后都要立即通知其它依赖的该信息的Widget更新，所以我们应该使用ChangeNotifierProvider，另外，这些信息改变后都是需要更新Profile信息并进行持久化的。综上所述，我们可以定义一个ProfileChangeNotifier基类，然后让需要共享的Model继承自该类即可，ProfileChangeNotifier定义如下：

```dart
class ProfileChangeNotifier extends ChangeNotifier {
  Profile get _profile => Global.profile;

  @override
  void notifyListeners() {
    Global.saveProfile(); // 保存Profile变更
    super.notifyListeners(); // 通知依赖的widget更新
  }
}
```

#### 用户状态

```dart
class UserModel extends ProfileChangeNotifier {
  User get user => _profile.user;

  // APP是否登录(如果有用户信息，则证明登录过)
  bool get isLogin => user != null;

  //用户信息发生变化，更新用户信息并通知依赖它的子孙Widgets更新
  set user(User user) {
    if (user?.login != _profile.user?.login) {
      _profile.lastLogin = _profile.user?.login;
      _profile.user = user;
      notifyListeners();
    }
  }
}
```

#### APP主题状态

```dart
class ThemeModel extends ProfileChangeNotifier {
  // 获取当前主题，如果为设置主题，则默认使用蓝色主题
  ColorSwatch get theme => Global.themes
      .firstWhere((e) => e.value == _profile.theme, orElse: () => Colors.blue);

  // 主题改变后，通知其依赖项，新主题会立即生效
  set theme(ColorSwatch color) {
    if (color != theme) {
      _profile.theme = color[500].value;
      notifyListeners();
    }
  }
}
```

#### APP语言状态
- 当APP语言选为跟随系统（Auto）时，在系通语言改变时，APP语言会更新；当用户在APP中选定了具体语言时（美国英语或中文简体），则APP便会一直使用用户选定的语言，不会再随系统语言而变。语言状态类定义如下

```dart
class LocaleModel extends ProfileChangeNotifier {
  // 获取当前用户的APP语言配置Locale类，如果为null，则语言跟随系统语言
  Locale getLocale() {
    if (_profile.locale == null) return null;
    var t = _profile.locale.split("_");
    return Locale(t[0], t[1]);
  }

  // 获取当前Locale的字符串表示
  String get locale => _profile.locale;

  // 用户改变APP语言后，通知依赖项更新，新语言会立即生效
  set locale(String locale) {
    if (locale != _profile.locale) {
      _profile.locale = locale;
      notifyListeners();
    }
  }
}
```