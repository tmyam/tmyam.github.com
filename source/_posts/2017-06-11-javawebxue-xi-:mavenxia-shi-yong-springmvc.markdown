---
layout: post
title: "Javaweb学习：Maven下使用SpringMVC"
date: 2017-06-11 14:18:00 +0800
comments: true
disqus: false
categories: IDEA Java Javaweb Maven SpringMVC
---

### 前言
对于Java web应用来说，SpringMVC已经越来越流行。这里就对SpringMVC环境的搭建做一个记录。

### 创建Maven Java web项目
这里可以参考以前的文章，这里就不多说了。
<!--more-->
### 创建目录
1、 在 **src/main/** 下创建 java 和 resources 目录。创建好的目录，程序是不识别的，我们需要进行一下配置：

![](/images/2017/06/11/01.jpg)
>**Sources** 一般用于标注类似 src 这种可编译目录。有时候我们不单单项目的 src 目录要可编译，还有其他一些特别的目录也许我们也要作为可编译的目录，就需要对该目录进行此标注。只有 Sources 这种可编译目录才可以新建 Java 类和包，这一点需要牢记。

>**Tests** 一般用于标注可编译的单元测试目录。在规范的 maven 项目结构中，顶级目录是 src，maven 的 src 我们是不会设置为 Sources 的，而是在其子目录 main 目录下的 java 目录，我们会设置为 Sources。而单元测试的目录是 src - test - java，这里的 java 目录我们就会设置为 Tests，表示该目录是作为可编译的单元测试目录。一般这个和后面几个我们都是在 maven 项目下进行配置的，但是我这里还是会先说说。从这一点我们也可以看出 IntelliJ IDEA 对 maven 项目的支持是比较彻底的。

>**Resources** 一般用于标注资源文件目录。在 maven 项目下，资源目录是单独划分出来的，其目录为：src - main -resources，这里的 resources 目录我们就会设置为 Resources，表示该目录是作为资源目录。资源目录下的文件是会被编译到输出目录下的。

>**Test Resources** 一般用于标注单元测试的资源文件目录。在 maven 项目下，单元测试的资源目录是单独划分出来的，其目录为：src - test -resources，这里的 resources 目录我们就会设置为 Test Resources，表示该目录是作为单元测试的资源目录。资源目录下的文件是会被编译到输出目录下的。

>**Excluded** 一般用于标注排除目录。被排除的目录不会被 IntelliJ IDEA 创建索引，相当于被 IntelliJ IDEA 废弃，该目录下的代码文件是不具备代码检查和智能提示等常规代码功能。

可以在 **hellotest.iml** 中看到目录被引入，可以被识别了。

``` xml
<content url="file://$MODULE_DIR$">
      <sourceFolder url="file://$MODULE_DIR$/src/main/java" isTestSource="false" />
      <sourceFolder url="file://$MODULE_DIR$/src/main/resources" type="java-resource" />
      <excludeFolder url="file://$MODULE_DIR$/target" />
    </content>
```

2、 添加依赖库
可以开始在 Maven 提供的 pom 文件(pom.xml) 添加我们spring的依赖，设置 pom.xml 前，需要到项目设置中 Maven autoload 勾选，否则无法自动下载 maven 依赖库(这是Intellij的坑)。

![](/images/2017/06/11/02.jpg)

在 pom.xml 添加 dependencies, plugins，以下是 pom.xml 设置后的完整文件：

``` xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.zhl.hellotest</groupId>
  <artifactId>hellotest</artifactId>
  <packaging>war</packaging>
  <version>1.0.0-SNAPSHOT</version>
  <name>hellotest Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!-- spring版本号 -->
    <spring.version>4.3.8.RELEASE</spring.version>
  </properties>


  <dependencies>
  <!--单元测试依赖 --> 
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>3.8.1</version>
    <!-- 表示开发的时候引入，发布的时候不会加载此包 --> 
    <scope>test</scope>
  </dependency>

  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>${spring.version}</version>
  </dependency>

  <!--spring单元测试依赖 --> 
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${spring.version}</version>
  </dependency>

  <!-- spring核心包 -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring.version}</version>
  </dependency>

  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>${spring.version}</version>
  </dependency>
  
  <!-- SpringMVC核心包 -->  
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${spring.version}</version>
  </dependency>

  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>${spring.version}</version>
  </dependency>

  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>${spring.version}</version>
  </dependency>
  </dependencies>

  <build>
    <finalName>hellotest</finalName>
  </build>
</project>
```
添加完成后，系统会自动下载所列出的依赖包，如果没有自动下载，可以在pom.xml文件中右键，选择Maven->Reimport载入。如下图：
![](/images/2017/06/11/03.jpg)

3、 进行SpringMVC配置
    * 首先在resource目录下新建一个spring的配置文件springmvc.xml，文件图片和内容如下：
![](/images/2017/06/11/04.jpg)

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 扫描指定包带@Controller注解的类-->
    <context:component-scan base-package="com.zhl.hellotest.controller"></context:component-scan>
    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>

</beans>
```
注意：如果没有加下面的内容，会导致错误：

``` text 
xmlns:context="http://www.springframework.org/schema/context"
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
```

* 在web.xml配置前端控制器，并加载springmvc.xml的配置文件，内容如下：

``` xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!--springMvc前端控制器-->
  <servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--contextConfigLocation配置springmvc加载的配置文件（配置处理器映射器、适配器等）-->
    <!--如果不配置，则默认加载/WEB-INFO/servlet名称-servlet.xml(springmvc-servlet.xml)-->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>

  <servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>

```
* 至此，文件配置完毕，下面我们用注解写一个方法测试一下，测试类放在springmvc.xml配置的扫描的包中，此处包名要与配置文件中扫描的包名一致。

创建包**com.zhl.hellotest.controller**,java文件**TestController**。
![](/images/2017/06/11/05.jpg)

**TestController**内容如下：

``` java
package com.zhl.hellotest.controller;

/** 注意不加下面的import，会导致出现找不到符号 Controller等的错误 */
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * Created by zhl on 2017/6/5.
 */
@Controller
public class TestController {
    @RequestMapping(value = "/admin")
    @ResponseBody
    public String testSpring(){
        return "My testSpring";
    }
}
```
此代码需要说明一下：
注解 @Controller 表示这是一个控制器，当请求来时将会扫描是否有匹配的RequestMapping。
注解 @RequestMapping 表示映射的路由，这里表示的是 /admin。
注解 @ResponseBody 表示返回的响应数据。

### 运行
* 编译，会出现如下错误：
![](/images/2017/06/11/06.jpg)

这里重新设置项目的java版本。
![](/images/2017/06/11/07.jpg)

![](/images/2017/06/11/08.jpg)

点击运行，打开浏览器后输入<http://localhost:8080/admin>，可以看到 **My testSpring**。
![](/images/2017/06/11/09.jpg)

### 参考
<http://www.tuicool.com/articles/aMzM7jR>
<http://blog.csdn.net/yege2006/article/details/51377674>
<http://blog.csdn.net/chunleixiahe/article/details/55001989>
<http://blog.csdn.net/u011275152/article/details/45242201>



