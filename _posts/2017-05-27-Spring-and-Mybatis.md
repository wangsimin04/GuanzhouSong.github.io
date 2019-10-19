---
layout: post
title:  SSM框架中的数据库调用
date:   2017-05-27 10:09:00 +0800
categories: 通天塔
tag: Java
---

* content
{:toc}


在公司需要用到ssm框架，于是从头开始学习。Sping框架对我来说可以算是比较神奇的，非常多的自动注入以及接口配置，一开始看项目还真有点云里雾里。

作为一个半吊子，我也只是依样画葫芦，按照前辈写好的方法复制过来进行修改。今天把controller，service之类的写好，
需要把数据库接入，于是仔细研究了一下神奇的mybatis。
>MyBatis是一个支持普通SQL查询，存储过程和高级映射的优秀持久层框架。
MyBatis消除了几乎所有的JDBC代码和参数的手工设置以及对结果集的检索封装。
MyBatis可以使用简单的XML或注解用于配置和原始映射，
将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录。

简单的说就是，用了Mybatis，不需要在程序内部反复写JDBC代码了，而是单独储存在一个XML文件里，需要修改的时候改一个xml配置文件就好了。
然而用起来简单，配置起来还真的不简单。就以公司项目为例写一下ssm框架里面是怎么读取数据库里的数据的。

因为对ssm理解尚浅，所以想从头到尾把ssm调用的整个过程模拟一下

## spring与spring mvc的调用请求

首先是页面请求，生成页面的时候请求
`d3.json("/data/ywclnode/doGetNodeAndLink.json?type=" + type, function (energy)`
于是spring就吭哧吭哧去所有的Controller里面查找这个链接对应的函数，并把**type**传递给函数。
![](/images/20170527102034.png)

找到了！在这个Controller中有个函数对应的是**/data/ywclnode**

![](/images/20170527102754.png)

然后有一个方法对应**doGetNodeAndLink.json**，加起来就是网页想要的函数的地址啦。

![](/images/20170527102634.png)

之后Controller就要调用相应的Service，以**doGetAllNodesJsonArray**为例，在Service里找到这个方法

![](/images/20170527103340.png)

发现需要调用**doGetDtYwclNodesDtoList**，也就是从数据库取到的数据来源，查找发现这个函数返回的其实是Mapper里的一个函数：

![](/images/微信截图_20170527113004.png)

Mapper其实只是一个接口，真正起作用的是mapper.xml

![](/images/微信截图_20170527135752.png)

真正起作用的mapper.xml中的SQL语句

![](/images/微信截图_20170527140104.png)

Mapper其实就是一个接口，也没有特殊的声明，怎么就突然能调用mapper.xml了呢？其实这就是Spring与Mybatis的配置文件在起作用。

下面看一下Mybatis与Spring是怎么交流的。

## Mybatis读取数据库

从最开始查看，首先去web.xml文件，发现spring配置文件调用的其实是spring-entry.xml而不是spring.xml。

![](/images/微信截图_20170527113318.png)

在spring-entry.xml中，可以看见首先定义了一个路径为spring.properties的文件，在最后又import了整个spring.xml文件。

![](/images/微信截图_20170527113508.png)

而spring.properties里面定义了众多的参数，包括数据库的链接，用户名以及密码。调用的时候直接读取这里的参数就可以了。

![](/images/微信截图_20170527113949.png)

链接Spring与Mybatis的终极大BOSS其实就是通用接口包扫描：

![](/images/微信截图_20170527133004.png)

```
<bean class="com.icinfo.framework.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.icinfo.frk.*.mapper"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
</bean>
```
通过通用接口包，MapperScannerConfigurer扫描basePackage下的mapper接口，封装为MapperFactoryBean注册给spring容器，当调用mapper接口时去DefaultSqlSessionFactory->Configuration获得接口类的代理类.

![](/images/微信截图_20170527133221.png)

而SqlSessionFactoryBean第一是解析mapper.xml配置，根据name组装Configuration对象，包装为DefaultSqlSessionFactory.

![](/images/微信截图_20170527134947.png)

同时SqlSessionFactory也解析Datasource，也就是主数据库，其中的参数`spring.jdbc.jdbcUrl`等均来自spring.properties。

![](/images/微信截图_20170527134749.png)

SqlSessionFactoryBean通过将Datasource与mapper.xml结合，完成数据库的读写。


## 结论

* **Spring的组成**：

Spring.xml+Spring.properties=Spring-entry.xml->web.xml

* **关于请求**：

页面->Controller->Service->Mapper->DataBase

* **Mybatis连接**：

MapperFactoryBean（Mapper接口）+SqlSessionFactoryBean（Mapper.xml）=DefaultSqlSessionFactory。

当调用mapper接口时只需要去DefaultSqlSessionFactory->Configuration获得接口类的代理类.


因为对Spring的理解还很浅显，可能上面的东西还存在问题。以后深入之后记得来更新改正。
