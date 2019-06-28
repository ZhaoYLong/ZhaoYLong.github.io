---
layout:     post
title:      Spring Boot项目《云收藏》复盘学习日志

subtitle:   从零到无限可能
date:       2019-06-14
author:     Yun.Sun.Long
header-img: img/post-bg-debug.png
catalog:    true
tag:
    - Java
    - Spring Boot
---

## 前言
这个项目源自[Spring Boot 中文导航](http://springboot.fun)的开源项目，首先对作者表示感谢。可以到[云收藏](http://favorites.ren/lookAround)这个网站去体验，具体的代码下载[Github](https://github.com/cloudfavorites/favorites-web)。

## 核心功能和使用的技术栈
- 核心功能
  - 收藏、分类、检索文章
  - 导出、导出（包活从浏览器中）
  - 可以点赞、分享、讨论
  - 注册、登录、个人账户
  - 临时收藏、查看别人收藏
  - 其它...

- 项目使用技术
  - Vue（前端技术框架）
  - Bootstrap（）
  - jQuery
  - Thymeleaf
  - Spring Data Jpa
  - Spring Boot Mail
  - WebJars
  - MySQL
  - Tomcat
  - Redis（2.0）
     - 1）引入依赖包； 2）添加配置文件； 3）添加Cache的配置类。

## 将项目布置到本地
- 因为我使用的是STS+maven，所以有以下几个步骤：
  - 下载
  - 导入项目
  - 选择maven/gradle项目管理工具
  - 选择project structure 查看项目依赖
  
## 云收藏部署方案
- 1）下载项目
- 2）修改application-pro.propertios文件对应的数据库信息
- 3）修改redis地址和静态文件地址
- 4）排除测试代码进行打包，mvn clearn package -Dmaven.test.skip=1
- 5）上传服务器启动

## 学习项目使用到的技术
- Thymeleaf 是跟velocity、freeMarker类似的模版引擎，完全替代JSP。
  - 特性：
    - 在有无网络环境下皆可运行，模版 + 数据
    - 开箱即用的特性
    - 提供Spring标准方言和一个与spring mvc完善集成的可选模块，可快速的实现表单绑定、属性编辑器、国际化等功能。

- Spring Boot Jpa(Java persistence API)，总的来说就是一套规范。
  - jpa是sun官方提出的Java持久化规范，Java开发人员提供了一种对象/关联映射工具，来管理Java应用中的关系数据。
  - Spring Boot Jpa是spring基于ORM框架、JPA规范封装的一套Jpa应用框架，使用户可以简单的编写代码来实现对数据的访问和操作。


# Spring资源推荐与面试题整理
- [Spring中文导航](http://springboot.fun/#resources)
- [spring注解](https://www.cnblogs.com/fashflying/p/6908028.html)
- [SpringCache与redis集成](https://www.cnblogs.com/chenkeyu/p/8028781.html)
  
- [史上最全69道Spring面试题和答案](https://blog.csdn.net/zl1zl2zl3/article/details/81865407)
- [springmvc和mybatis面试题（含答案）](https://blog.csdn.net/xinghuo0007/article/details/53463897)
- [松哥整理了 15 道 Spring Boot 高频面试题，看完当面霸](https://blog.csdn.net/u012702547/article/details/92803816#comments)
- [Spring Boot面试题整理](https://blog.csdn.net/Time_sg/article/details/81589660)

- [IBM Java课程](https://www.ibm.com/developerworks/cn/java/intro-to-java-course/index.html)

# 其他资源推荐
- [正则大全](https://any86.github.io/any-rule/)
- [java对redis的基本操作](https://www.cnblogs.com/edisonfeng/p/3571870.html)

> 未完待续...