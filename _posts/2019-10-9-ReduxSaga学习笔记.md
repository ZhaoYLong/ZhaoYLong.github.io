---
layout:     post
title:      Redux-Saga是什么？

subtitle:   没错，这又是一篇学习笔记
date:       2019-10-09
author:     Laqudee.Z
header-img: img/post-bg-debug.png
catalog: false
tags:
    - Front End
    - Redux
---

- [英文原版官网](https://redux-saga.js.org)
- [redux-saga中文教程官网](https://redux-saga-in-chinese.org)
<br/>

- redux-saga：用于管理应用程序Side Effect(副作用，异步获取数据，访问浏览器缓存)的库
  - 目的：让副作用管理更容易，执行更高效，测试更简单，处理错误更容易
  - saga可以比喻成一个应用程序中一个单独的线程，独自负责处理副作用。
  - redux-saga是一个redux meddlewear，意味着这个线程可以通过正常的redux action从主应用程序启动，暂停和取消，能访问完整的redux state，也可以dispathch redux action

- redux-saga使用ES6的Generator功能，让异步的流程更易于读取，写入和测试。
  
- 安装 

```
$ npm install --save redux-saga
# or
yarn add redux-saga
```

- 示例：
  - 假设我们有一个UI界面，在单击按钮时从远层服务器获取一些用户数据
  
```js
class UserComponent extends React.Component {
  ...
  onSomeButtonClicked(){
      const {useID, dispatch} = this.props
      dispatch((type: 'USER_FETCH_REQUESTED',payload: {userId}))
  }  
  ...
}
```

> 未完待续...