---
layout: post
title: "[笔记][Spring实战]-装配Bean"
date: 2017-09-13
categories: Spring
tags: Java, Spring
---
* content
{:toc}


&emsp;&emsp;Spring框架的核心是Spring容器，容器负责管理应用中组件的生命周期，它会创建这些组件
并保证它们的依赖能够得到满足（Spring容器负责创建应用程序中的 bean 并通过DI来协调这些对象之间的关系），
这样的话，组件才能完成预定的任务。Spring提供了三种主要的装配机制：
  + 在XML中进行显式配置
  + 在Java中进行显式配置
  + 隐式的bean发现机制和自动装配  

&emsp;&emsp;建议尽可能地使用自动配置的机制，显式配置越少越好。当你必须要显式配置 bean 的时候（比如，
有些源码不是由你来维护的，而当你需要为这些代码配置 bean 的时候）

<!--More-->



## 自动化装配bean
Spring从两个角度来实现自动化装配：
 - 组件扫描（component scanning）：Spring会自动发现应用上下文中所创建的 bean 。
 - 自动装配（autowiring）：Spring自动满足 bean 之间的依赖。  

### 创建可被发现的bean  
<div style="background-color:#5F9EA0">程序清单 CompactDisc接口在Java中定义了CD的概念</div>
```java
package soundsystem;
public interface CompactDisc {
    void play();
}
```
<div style="background-color:#5F9EA0">程序清单 带有@Component注解的CompactDisc实现类SgtPeppers</div>
```java
package soundsystem;
import org.springframework.stereotype.Component;

@Component
public class SgtPeppers implements CompactDisc {
    private String title = "Sgt. Perppers' Lonely Hearts Club Band";
    private String artist = "The Beatles";
    public void play() {
        System.out.println("Playing " + title + " by " + artist);
    }
}
```
SgtPeppers类上使用了@Component注解，这个简单的注解表明该类会作为组件类，并告知Spring
要为这个类创建bean。
组件扫描默认是不启用的，我们需要显示配置一下Spring，从而命令它去寻找带有@Component注解的类，
并为其创建bean。完成这项任务Spring提供了两种方式：
  * 基于Java配置文件
  <div style="background-color:#5F9EA0">程序清单 @ComponentScan 注解启用了组件扫描</div>
  ```java
  package soundsystem;
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;

  @Configuration // 表示这个.java文件是一个配置文件
  @ComponentScan // 表示开启Component扫描
  public class CDPlayerConfig {
  }
  ```

  * 基于XML配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

  <context:component-scan base-package="soundsystem" />
  </beans>
  ```
  默认情况下，```<context:component-scan>```查找使用构造型（stereotype）注解所标注的类。  
  这些特殊注解如下：
  - @Component——通过的构造型注解，标识该类为Spring组件
  - @Controller——标识将该类定义为Spring MVC controller
  - @Repository——标识将该类定义为数据仓库
  - @Service——标识将该类定义为服务
  - 使用@Component标注的任意自定义注解

  通过为```<context:component-config>```配置子元素```<context:include-filter>```
  或者```<context:exclude-filter>```来调整扫描行为。  
  ```<context:include-filter>``` 的type和expression属性一起来定义组件扫描策略。  

  | 过滤器类型 | 描述 |
  | ------------- |:-------------|
  | annotation | 过滤器扫描使用指定注解所标注的那些类，通过expression属性指定要扫描的注解 |
  | assignable |	过滤器扫描派生于expression属性所指定类型的那些类 |
  | aspectj |	过滤器扫描与expression属性所指定的AspectJ表达式所匹配的那些类 |
  | custom |	使用自定义的org.springframework.core.type.TypeFilter实现类，该类由expression属性指定 |
  | regex	| 过滤器扫描类的名称与expression属性所指定的正则表达式所匹配的那些类 |

  例如：
  ```xml
  <context:component-scan base-package="com.gve.vcs.web" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Component"/>
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/>
  </context:component-scan>
  ```


### 为组件扫描的bean命名
```java
@Component("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
  ...
}
```
### 设置组件扫描的基础包

```@ComponentScan``` 没有其他，默认会扫描与配置类相同的包。有一个原因会促使我们明确设置基础包，
就是我们想要将配置类放在单独的包中，使其与其他的应用代码区分开来。  

```
@ComponentScan("soundsystem")
```
```
@ComponentScan(basePackages = "soundsystem")
```
```
@ComponentScan(basePackages = {"soundsystem", "video"})
```
```
@ComponentScan(basePackageClasses = {CDPlayer.class, DVDPlayer.class})
```
### 通过为bean添加注解实现自动装配
简单得说，自动装配的意思是让Spring从应用上下文中找到对应的bean的引用，并将它们注入到指定的bean。
通过@Autowired注解可以完成自动装配。  
考虑下面代码中的CDPlayer类，它的构造函数被@Autowired修饰，表明当Spring创建CDPlayer的bean时，会给这个构造函数传入一个CompactDisc的bean对应的引用。  
```java
package soundsystem;
import org.springframework.beans.factory.annotation.Autowired;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc cd;

    @Autowired
    public CDPlayer(CompactDisc cd) { this.cd = cd; }
    public void play() { cd.play(); }
}

```
还有别的实现方法，例如将@Autowired注解作用在setCompactDisc()方法上:  
```java
@Autowired
public void setCd(CompactDisc cd) {
    this.cd = cd;
}
```
更简单的用法是，可以将@Autowired注解直接作用在成员变量之上，例如：
```java
@Autowired
private CompactDisc cd;
```

### 4种类型的自动装配
 + byName: 把与Bean的属性具有相同名字（或者ID）的其他Bean自动装配到Bean的对应属性中。
 如果没有跟属性的名字相匹配的Bean，则该属性不进行装配。

 + byType: 把与Bean的属性具有相同类型的其他Bean自动装配到Bean的对应属性中。
 如果没有跟属性的名字相匹配的Bean，则该属性不进行装配。

 + constructor: 把与Bean的构造器入参具有相同类型的其他Bean自动装配到Bean构造器的对应入参中。

 + autodetect: 首先尝试使用constructor进行自动装配。如果失败，再尝试使用byType进行自动装配。


## 通过Java代码装配bean

## 通过XML装配bean
