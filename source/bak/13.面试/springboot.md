---
title: Spring Boot
date: 2021-11-21
keywords: Spring Boot
description:  Spring Boot
tags:
  - Spring Boot
categories:
  - Spring Boot
---


## 什么是 Spring Boot？

Spring Boot 是 Spring 开源组织下的子项目，是 Spring 组件一站式解决方案，主要是简化了使用 Spring 的难度，简省了繁重的配置，提供了各种启动器，开发者能快速上手。

## 为什么要用 Spring Boot

- 独立运行
- 简化配置
- 自动配置
- 无代码生成和XML配置
-  应用监控
- 上手容易

## Spring Boot 的核心配置文件有哪几个？它们的区别是什么?

Spring Boot 的核心配置文件是 application 和 bootstrap 配置文件。

application 配置文件这个容易理解，主要用于 Spring Boot 项目的自动化配置。

bootstrap 配置文件有以下几个应用场景。

- 使用 Spring Cloud Config 配置中心时，这时需要在 bootstrap 配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
- 一些固定的不能被覆盖的属性；
- 一些加密/解密的场景；

## Spring Boot 的配置文件有哪几种格式？它们有什么区别？

.properties 和 .yml，它们的区别主要是书写格式不同。


## Spring Boot 的核心注解是哪个？它主要由哪几个注解组成的？

启动类上面的注解是@SpringBootApplication，它也是 Spring Boot 的核心注解，主要组合包含了以下 3 个注解：

@SpringBootConfiguration：组合了 @Configuration 注解，实现配置文件的功能。

@EnableAutoConfiguration：打开自动配置的功能，借助@Import的支持，收集和注册特定场景相关的bean定义。最关键的要属@Import(EnableAutoConfigurationImportSelector.class)，借助EnableAutoConfigurationImportSelector，@EnableAutoConfiguration可以帮助SpringBoot应用将所有符合条件的@Configuration配置都加载到当前SpringBoot创建并使用的IoC容器。也可以关闭某个自动配置的选项，如关闭数据源自动配置功能： @SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })。

@ComponentScan：Spring组件扫描。

- 当我们运行SpringApplication的main方法时,调用静态方法run()首先是实例化,SpringApplication初始化的时候主要做主要做三件事：
  - 根据classpath下是否存在(ConfigurableWebApplicationContext)判断是否要启动一个web applicationContext。
  - SpringFactoriesInstances加载classpath下所有可用的ApplicationContextInitializer
  - SpringFactoriesInstances加载classpath下所有可用的ApplicationListener
- SpringApplicatio实例化完成并且完成配置后调用run()方法,首先遍历初始化过程中加载的SpringApplicationRunListeners，然后调用starting(),开始监听springApplication的启动。
- 加载SpringBoot配置环境(ConfigurableEnvironment)，如果是通过web容器发布，会加载StandardEnvironment。将配置环境(Environment)加入到监听器对象中(SpringApplicationRunListeners)。
- banner属性的设置
- ConfigurableApplicationContext(应用配置上下文)创建，根据webEnvironment是否是web环境创建默认的contextClass，AnnotationConfigEmbeddedWebApplicationContext(通过扫描所有注解类来加载bean)和ConfigurableWebApplicationContext),最后通过BeanUtils实例化上下文对象，并返回。
-  prepareContext()方法将listeners、environment、applicationArguments、banner等重要组件与上下文对象关联。
- refreshContext(context),bean的实例化完成IoC容器可用的最后一道工序。
 
## 开启 Spring Boot 特性有哪几种方式?

- 继承spring-boot-starter-parent项目
- 导入spring-boot-dependencies项目依赖

## Spring Boot 需要独立的容器运行吗？

可以不需要，内置了 Tomcat/ Jetty 等容器。

## 运行 Spring Boot 有哪几种方式？

- 打包用命令或者放到容器中运行
- 用 Maven/ Gradle 插件运行
- 直接执行 main 方法运行

## Spring Boot 自动配置原理是什么？

注解 @EnableAutoConfiguration, @Configuration, @ConditionalOnClass 就是自动配置的核心，首先它得是一个配置文件，其次根据类路径下是否有这个类去自动配置。

## 你如何理解 Spring Boot 中的 Starters？

Starters可以理解为启动器，它包含了一系列可以集成到应用里面的依赖包，你可以一站式集成 Spring 及其他技术，而不需要到处找示例代码和依赖包。如你想使用 Spring JPA 访问数据库，只要加入 spring-boot-starter-data-jpa 启动器依赖就能使用了。

## 如何在 Spring Boot 启动的时候运行一些特定的代码

可以实现接口 ApplicationRunner 或者 CommandLineRunner，这两个接口实现方式一样，它们都只提供了一个 run 方法

## Spring Boot 支持哪些日志框架？推荐和默认的日志框架是哪个?

Spring Boot 支持 Java Util Logging, Log4j2, Lockback 作为日志框架，如果你使用 Starters 启动器，Spring Boot 将使用 Logback 作为默认日志框架

## 保护 Spring Boot 应用有哪些方法

- 在生产中使用HTTPS
- 使用Snyk检查你的依赖关系
- 升级到最新版本
- 启用CSRF保护
- 使用内容安全策略防止XSS攻击

## SpringFactoriesLoader

这里简单分析一下 SpringFactoriesLoader 这个工具类的使用。它其实和 java 中的 SPI 机制的原理是一样的，不过它比 	SPI 更好的 点在于不会一次性加载所有的类，而是根据 key 进行加 载。

首 先 ， SpringFactoriesLoader 的 作 用 是 从 classpath/META-INF/spring.factories 文件中，根据 key 来 加载对应的类到 spring IoC 容器中。


