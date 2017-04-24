---
layout: post
title:  "java.lang.ClassCastException: org.slf4j.impl.SimpleLoggerFactory cannot be cast to ch.qos.logback.classic.LoggerContext"
date:   2017-04-24 12:45:45
categories: Java
tags: Java ClassCastException
---

* content
{:toc}

## 错误信息
```
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/C:/Users/Administrator/.m2/repository/org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/C:/Users/Administrator/.m2/repository/ch/qos/logback/logback-classic/1.1.2/logback-classic-1.1.2.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
[INFO] Initializing logback from [E:\workspaces\gvesys-all\app-api\src\main\webapp\WEB-INF\logback.xml]
[ERROR] Failed startup of context org.mortbay.jetty.plugin.Jetty6PluginWebAppContext@68de8522{/app-api,E:\workspaces\gvesys-all\app-api\src\main\webapp}
java.lang.ClassCastException: org.slf4j.impl.SimpleLoggerFactory cannot be cast to ch.qos.logback.classic.LoggerContext
	at com.gve.vcs.webcomn.log.config.AbstractLogbackCofigurer.initLogging(AbstractLogbackCofigurer.java:34)
	at com.gve.vcs.webcomn.log.config.LogbackWebConfigurer.initLogging(LogbackWebConfigurer.java:48)
	at com.gve.vcs.webcomn.log.config.LogbackWebConfigurer.initLogging(LogbackWebConfigurer.java:70)
	at com.gve.vcs.webcomn.log.listener.LogbackConfigListener.contextInitialized(LogbackConfigListener.java:20)
	at org.mortbay.jetty.handler.ContextHandler.startContext(ContextHandler.java:549)
	at org.mortbay.jetty.servlet.Context.startContext(Context.java:136)
    ....
```

## 分析
代码可以定位到：`LoggerContext context = (LoggerContext) LoggerFactory.getILoggerFactory();`， 这里`slf4j-simple`和`logback-classic`（还可能会有`slf4j-log4j12`）都实现了`org.slf4j.impl.StaticLoggerBinder`。

## 解决方法
如果要使用logback，需要修改POM文件，排除`slf4j-simple`和`slf4j-log4j12`的引用，例如：
```
<dependency>
  <groupId>org.apache.kafka</groupId>
  <artifactId>kafka_2.8.2</artifactId>
    <version>0.8.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```
至于如何定位，可以使用IntelliJ IDEA得`Show Dependencies`。
