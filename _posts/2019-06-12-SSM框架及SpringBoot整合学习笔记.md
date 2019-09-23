---
layout:     post
title:      SSM框架及Spring Boot整合学习

subtitle:   从零到负无穷
date:       2019-06-12
author:     Yun.Long
header-img: img/post-bg-debug.png
catalog:    true
tag:
    - Spring
    - Java
    - SSM
---
## 1、基本概念
### 1.1 Spring
  是开源框架，一个轻量级的Java开发框架。为了解决企业应用开发的复杂性而创建的。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。   
  简单来说，Spring是一个轻量级的控制反转（IoC）和面向切面（AOP）的容器框架。   

  但与我们平时开发接触最多的估计就是IOC容器，它可以装载bean（也就是我们java中的类，当然也包括service dao里面的），有了这个机制，我们就不用在每次使用这个类的时候为它初始化，很少看到关键字new。另外spring的aop，事务管理等等都是我们经常用到的。   

### 1.2 SpringMVC
Spring MVC属于SpringFrameWork的后续产品，已经融合在Spring Web Flow里面。Spring MVC分离了控制器、模型对象、分派器以及处理对象的角色，这种分离让它们更容易进行定制。    

主要用于web层，相当于controller(等价于传统的servlet和struts的action)，用来处理用户请求。举个例子：用户在地址栏中输入https://网站域名/login。那么spring mvc就会拦截到这个请求，并且调用controller层中相应的方法，（中间可能包含验证用户名和密码的业务逻辑，以及查询数据库操作，但这些都不是springmvc的职责），最终把结果返回给用户，并返回相应的页面（当然也可以只返回json/xml等格式数据）。springmvc就是做前面和后面过程的活，与用户打交道！！

### 1.3 MyBatis
MyBatis本是aoache的2一个开源项目iBatis，MyBatis是一个基于Java的持久层框架。   

iBatis提供的持久层框架包括SQL Maps和Data Access Object是（DAO） 。   

MyBatis消除了几乎所有的JDBC代码和参数的手工设置以及结果集的检索。MyBatis 使用简单的 XML或注解用于配置和原始映射，将接口和 Java 的POJOs（Plain Old Java Objects，普通的 Java对象）映射成数据库中的记录。  

可以自由的控制sql，编写的代码可以提升数据库访问的效率；再者，mybatis可以使用xml的方式来组织管理我们的sql，因为一般程序出错很多情况是sql出错。

> [推荐阅读「移动商城项目总结」](https://segmentfault.com/a/1190000013922256)

## 2、Spring之常见问题
### 2.1 什么是Spring框架？
指的都是SpringFramework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发。这些模块是：核心容器、数据访问/集成、Web、AOP（面向切面编程）、工具、消息和测试模块。   
- Core Container中的Core组件是Spring所有组件的核心   
- Beans 组件和Context组件是实现IOC和依赖注入的基础，AOP组件用来实现面向切面编程。  

Spring的6个特性：  
- 核心技术：依赖注入（DI），AOP，事件（events），资源，i18n，验证，数据绑定，类型转换，SpEL。   
- 测试：模拟对象，TestContext框架，Spring MVC测试，WebTestClient。
- 数据访问：事务，DAO支持，JDBC，ORM，编组XML。  
- Web支持：Spring MVC和Spring Web Flux Web 框架。
- 集成：远程处理，JMS，JCA，JMX，电子邮件，任务，调度，缓存。
- 语言：kotlin \ groovy  \ 动态语言。

### 2.2 重要的Spring模块？   
下图是Spring4.x版本，目前最新的5.x版本中 Web 模块的 Portlet 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件。 

![图一](/img/spring-01.png) 

相关模块解释：     
- Spring Core：基础，Spring其他所有的功能都需要依赖于该类库。主要提供IOC依赖注入功能。
- Spring Aspects：该模块为与AspectJ的集成提供支持。
- Spring AOP：提供了面向切面的编程实现。
- Spring JDBC：Java数据库连接。
- Spring JMS：Java消息服务。
- Spring ORM：用于支持Hibernate等ORM工具。
- Spring Web：为创建Web应用程序提供支持。
- Spring Test：提供了对JUnit和TestNG测试的支持。

### 2.3 对于Spring IoC 和 AOP 的理解？
#### IoC
Inverse of Control：控制反转。是一种设计思路，就是将原本在程序中手动创建对象的控制权，交由Spring框架来管理。IoC在其他语言中也有应用，并非Spring特有。  
IoC容器是Spring用来实现IoC的载体，IoC容器实际上就是个Map(key, value),Map中存放的是各种对象。  

将对象之间的相互依赖关系交给IoC容器管理，并由IoC容器完成对象的注入。这样可以很大程度上简化应用的开发。   
IoC容器就像一个工厂，当我们需要创建一个对象时，只需要配置好配置文件/注解即可，不用考虑对象是怎么创建出来的。

Spring时代，我们一般通过xml文件来配置Bean，后来xml文件配置不太好，Spring Boot注解配置就慢慢开始流行起来。

#### Spring IoC 的初始化过程：

![图二](/img/spring-02.png)

[IoC源码阅读](https://javadoop.com/post/spring-ioc)

#### AOP
Aspect-Oriented Programming:面向切面编程。能够将哪些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可扩展性和可维护性。

Spring AOP就是基于动态代理，如果要代理的对象，实现了某个接口，那么Spring AOP会使用JDK Proxy，去创建代理对象，而对于没有实现接口的对象，就无法使用JDK Proxy去进行代理，这时候Spring AOP 会使用Cglib生成一个被代理对象的子类作为代理，如下图：  

![图三](/img/spring-03.jpg)

当然你也可以使用 AspectJ ,Spring AOP 已经集成了AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。  

使用 AOP 之后我们可以把一些通用功能抽象出来，在需要用到的地方直接使用即可，这样大大简化了代码量。我们需要增加新功能时也方便，这样也提高了系统扩展性。日志功能、事务管理等等场景都用到了 AOP 。

#### Spring AOP 和 AspectJ AOP 有什么区别？
- 前者属于运行时增强，而后者时编译时增强。 
- Spring AOP 基于代理（Proxying），而 AspectJ基于字节码操作（Bytecode Manipulation）.
- Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单.
- 若我们的切面比较少，那么两者性能差异不大。但是，当切面太多的化，最好选择AspectJ， 它比Spring AOP 快很多。

### 2.4 Spring 中的 bean的作用域有哪些？
- singleton:唯一bean实例，Spring中的bean默认都是单例的。
- protorypt:每次请求都会创建一个新的bean实例。
- request：每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。
- session：每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP session内有效。
- global-session： 全局session作用域，仅仅在基于portlet的web应用中才有意义，Spring5已经没有了。Portlet是能够生成语义代码(例如：HTML)片段的小型Java Web插件。它们基于portlet容器，可以像servlet一样处理HTTP请求。但是，与 servlet 不同，每个 portlet 都有不同的会话。

### 2.5 Spring 中的单例bean的线程安全问题
单例bean存在线程问题，主要是因为当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作会存在线程安全问题。    
常见有两种解决方法： 
- 1.在Bean对象中尽量避免定义可变成员变量（不太现实）
- 2.在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中（推荐）

### 2.6 Spring 中的bean生命周期？

- Bean 容器找到配置文件中Spring Bean的定义。
- Bean 容器利用Java Reflection API 创建一个Bean的实例。
- 若涉及到一些属性值利用set()方法设置一些属性值。
- 若Bean 实现了 BeanNameAware接口，调用setBeanName()方法，传入Bean的名字。
- 若Bean 实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 若Bean 实现了BeanFactoryAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 与上面的类似，如果实现了其他*.Aware接口，就调用相应的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessBeforeInitialization()方法
- 如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法。
- 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
- 如果有和加载这个 Bean的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessAfterInitializatio()方法.
- 当要销毁Bean的时候，若Bean实现了DisposableBean接口，执行destroy()方法。
- 当要销毁Bean的时候，若Bean在配置文件中的定义包括destroy-method属性，执行指定的方法。
图示：  

![图四](/img/spring-04.jpg)

与之类似的中文版本：   

![](/img/spring-05.jpg)

### 2.7 Spring MVC 
在此之前，先要谈一谈在Spring MVC时代之前的Model1和Model2   
- Model1:
  - 很多学 Java 后端比较晚的朋友可能并没有接触过 Model1 模式下的 JavaWeb 应用开发。在 Model1 模式下，整个 Web 应用几乎全部用 JSP 页面组成，只用少量的 JavaBean 来处理数据库连接、访问等操作。这个模式下 JSP 即是控制层又是表现层。显而易见，这种模式存在很多问题。比如①将控制逻辑和表现逻辑混杂在一起，导致代码重用率极低；②前端和后端相互依赖，难以进行测试并且开发效率极低；  

- Model2:
  - 学过 Servlet 并做过相关 Demo 的朋友应该了解“Java Bean(Model)+ JSP（View,）+Servlet（Controller） ”这种开发模式,这就是早期的 JavaWeb MVC 开发模式。Model:系统涉及的数据，也就是 dao 和 bean。View：展示模型中的数据，只是用来展示。Controller：处理用户请求都发送给 ，返回数据给 JSP 并展示给用户。

Model2 模式下还存在很多问题，Model2的抽象和封装程度还远远不够，使用Model2进行开发时不可避免地会重复造轮子，这就大大降低了程序的可维护性和复用性。于是很多JavaWeb开发相关的 MVC 框架营运而生比如Struts2，但是 Struts2 比较笨重。随着 Spring 轻量级开发框架的流行，Spring 生态圈出现了 Spring MVC 框架， Spring MVC 是当前最优秀的 MVC 框架。相比于 Struts2 ， Spring MVC 使用更加简单和方便，开发效率更高，并且 Spring MVC 运行速度更快。

- MVC 
  - 是一种设计模式，Spring MVC是一款优秀的MVC框架。Spring MVC可以帮助我们进行更简洁的web层开发，天生与Spring框架集成。Spring MVC 下，一般把后段项目分为service层（处理业务）、Dao层（数据库操作）、Entity层（实例类）、Controller层（控制层，返回数据给前台页面）。

- Spring MVC的简单原理图：   

![图六](/img/spring-06.jpg)

### 2.8 Spring MVC工作元理

![图七](/img/spring-07.jpg)

上图的一个笔误的小问题：Spring MVC 的入口函数也就是前端控制器 DispatcherServlet 的作用是接收请求，响应结果。

流程说明（重要）：  
- 1.客户端（浏览器）发送请求，直接请求到DispatcherServlet.
- 2.DispatcherServlet根据请问信息调用HandlerMapping，解析请求对应的handler。
- 3.解析到对应的Handler（也就是我们平常说的Controller控制器）后，开始由HandlerAdapter适配器处理。
- 4.HandlerAdapter会根据Handler来调用真正的处理器开始处理请求，并返回数据对象，逻辑。
- 5.处理器处理完业务后，会返回一个ModelAndView对象，Model是返回的数据对象，View是个逻辑上的View。
- 6.ViewResolver会根据逻辑view查找实际的view。
- 7.DispaterServlet把返回的Model传给view（视图渲染）。
- 8.把view返回给请求者（浏览器）。

### 2.9 Spring 框架中用到的设计模式？
- 工厂模式：Spring通过BeanFactory、ApplicationContext创建bean对象来使用工厂模式。
- 代理设计模式：Spring AOP功能的实现。
- 单例设计模式：Spring中的Bean默认都是单例的。
- 模版方法模式：Spring中jdbcTemplate、hibernateTemplate等以Template结尾的对数据库操作的类，它们就使用到了模版模式。
- 包装器设计模式：我们的项目需要连接多个数据库，而且不同的客户在每一次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求来动态切换不同的数据源。
- 观察者模式：Spring事件驱动模型就是观察者模式很经典的一个应用。
- 适配器模式：Spring AOP的增强或通知（Advice）使用到了适配器模式、Spring MVC中也用到了适配器模式适配Cotroller.
-  ...

### 2.10 @Component和@Bean的区别是什么？
- 1.作用对象不同：
  - @Component注解作用于类
  - @Bean注解作用于方法。
  
- 2.
  - @Component通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中（我们可以使用 @ComponentScan 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）
  - @Bean注解通常是我们在标有该注解的方法中定义产生这个备案，@Bean告诉了Spring这是某个类的实例，当我需要用它的时候还给我。

- 3.
  - @Bean 注解比 Component 注解的自定义性更强，而且很多地方我们只能通过 @Bean 注解来注册bean。比如当我们引用第三方库中的类需要装配到 Spring容器时，则只能通过 @Bean来实现。

@Bean注解使用实例：    
```java
@Configuration
public class AppConfig{
  @Bean
  public TransferService transferService(){
    return new TransferServiceImpl();
  }
}
```

上面的代码相当于下面的xml配置：    
```xml
<beans>
  <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

下面这个例子是通过@Component无法实现的。   
```java
@Bean
public OneService getService(status){
  case(status){
    when 1:
          return new serviceImpl1();
    when 2:
          return new serviceImpl2();
    when 3:
          return new serviceImpl3();
  }
}
```

### 2.11 将一个类声明为Spring的bean得注解有哪些？
我们一般使用@Autowired注解自动装配bean，要想把类标识成可用于@Autowired注解自动装配的bean的类，采用以下注解可实现：  
- @Component：通用的注解，可标注任意类为 Spring 组件。如果一个Bean不知道属于拿个层，可以使用@Component 注解标注。
- @Repository：对应持久层即 Dao 层，主要用于数据库相关操作。
- @Service：对应服务层，主要涉及一些复杂的逻辑，需要用到Dao层。
- @Controller：对应Spring MVC控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

### 2.12 Spring管理事务的方式有几种？
1、编程式事务，在代码中硬编码。（不推荐使用）    
2、声明式事务，在配置文件中配置（推荐使用）   

声明式事务又分为两种：   
- 1.基于XML的声明式事务
- 2.基于注解的声明式事务

### 2.13 Spring事务中的隔离级别有哪几种？
- TransactionDefinition.ISOLATION_DEFAULT: 使用后端数据库默认的隔离级别，Mysql 默认采用的 REPEATABLE_READ隔离级别 Oracle 默认采用的 READ_COMMITTED隔离级别.
  
- TransactionDefinition.ISOLATION_READ_UNCOMMITTED: 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读

- TransactionDefinition.ISOLATION_READ_COMMITTED: 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生

- TransactionDefinition.ISOLATION_REPEATABLE_READ: 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。

- TransactionDefinition.ISOLATION_SERIALIZABLE: 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

### 2.14 Spring事务中哪几种事务传播行为/
支持当前事务的情况：   
- TransactionDefinition.PROPAGATION_REQUIRED： 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- TransactionDefinition.PROPAGATION_SUPPORTS： 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- TransactionDefinition.PROPAGATION_MANDATORY： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

不支持当前事务的情况：  
- TransactionDefinition.PROPAGATION_REQUIRES_NEW： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- TransactionDefinition.PROPAGATION_NOT_SUPPORTED： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- TransactionDefinition.PROPAGATION_NEVER： 以非事务方式运行，如果当前存在事务，则抛出异常。

其他情况：  
- TransactionDefinition.PROPAGATION_NESTED： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

## 3 Spring云收藏复盘记录
### 3.1 前言
这个项目源自[Spring Boot 中文导航](http://springboot.fun)的开源项目，首先对作者表示感谢。可以到[云收藏](http://favorites.ren/lookAround)这个网站去体验，具体的代码下载[Github](https://github.com/cloudfavorites/favorites-web)。

### 3.2 核心功能和使用的技术栈
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

### 3.3 将项目布置到本地
- 因为我使用的是STS+maven，所以有以下几个步骤：
  - 下载
  - 导入项目
  - 选择maven/gradle项目管理工具
  - 选择project structure 查看项目依赖
  
### 3.4云收藏部署方案
- 1）下载项目
- 2）修改application-pro.propertios文件对应的数据库信息
- 3）修改redis地址和静态文件地址
- 4）排除测试代码进行打包，mvn clearn package -Dmaven.test.skip=1
- 5）上传服务器启动

### 3.5 学习项目使用到的技术
- Thymeleaf 是跟velocity、freeMarker类似的模版引擎，完全替代JSP。
  - 特性：
    - 在有无网络环境下皆可运行，模版 + 数据
    - 开箱即用的特性
    - 提供Spring标准方言和一个与spring mvc完善集成的可选模块，可快速的实现表单绑定、属性编辑器、国际化等功能。

- Spring Boot Jpa(Java persistence API)，总的来说就是一套规范。
  - jpa是sun官方提出的Java持久化规范，Java开发人员提供了一种对象/关联映射工具，来管理Java应用中的关系数据。
  - Spring Boot Jpa是spring基于ORM框架、JPA规范封装的一套Jpa应用框架，使用户可以简单的编写代码来实现对数据的访问和操作。


## 4 Spring资源推荐与面试题整理
- [Spring中文导航](http://springboot.fun/#resources)
- [spring注解](https://www.cnblogs.com/fashflying/p/6908028.html)
- [SpringCache与redis集成](https://www.cnblogs.com/chenkeyu/p/8028781.html)
  
- [史上最全69道Spring面试题和答案](https://blog.csdn.net/zl1zl2zl3/article/details/81865407)
- [springmvc和mybatis面试题（含答案）](https://blog.csdn.net/xinghuo0007/article/details/53463897)
- [松哥整理了 15 道 Spring Boot 高频面试题，看完当面霸](https://blog.csdn.net/u012702547/article/details/92803816#comments)
- [Spring Boot面试题整理](https://blog.csdn.net/Time_sg/article/details/81589660)

- [IBM Java课程](https://www.ibm.com/developerworks/cn/java/intro-to-java-course/index.html)

## 5 其他资源推荐
- [正则大全](https://any86.github.io/any-rule/)
- [java对redis的基本操作](https://www.cnblogs.com/edisonfeng/p/3571870.html)


> 本文详细阅读和参照了[小专栏-Java专栏](https://xiaozhuanlan.com/)与其他资料。