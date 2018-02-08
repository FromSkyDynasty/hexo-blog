---
title: 初识Java9模块化系统
date: 2018-02-08 14:40:46
tags:
    - java9
    - jpms
    - 模块化
---

java9在2017年9月21日正式发布，添加了很多[新特性](https://www.ibm.com/developerworks/cn/java/the-new-features-of-Java-9/index.html)，最让我感兴趣的就是模块系统(JPMS - Java 9 Platform Module System )。

<!-- more -->

在我的理解下，这个模块系统与[OSGi](https://zh.wikipedia.org/wiki/OSGi)相似，对比OSGi,个人感觉配置比OSGi简单很多（毕竟是集成在JDK中了）

### 什么是模块

> Modularity adds a higher level of aggregation above packages. The key new language element is the module—a uniquely named, reusable group of related packages, as well as resources (such as images and XML files) and a module descriptor specifying

> 模块化在package上添加了更高层次的聚合, 新的关键字是`module`: 一个唯一命名的，可重用的相关包组，以及资源（如图像和XML文件）和一个模块描述符

引用自[Understanding Java 9 Modules](https://www.oracle.com/corporate/features/understanding-java-9-modules.html)

* [the module’s name](#module-name)
* [the module’s *dependencies*](#module-dependency) (that is, other modules this module depends on)
* [the packages it explicitly makes available to other modules](#module-exports) (all other packages in -     the module are implicitly unavailable to other modules)
* [the *services it offers*](#module-exports)
* [the *services it consumes*](#module-consumer)
* to what other modules it allows reflection

<h3 id="module-name">the module’s name(模块名称)</h3>

模块名称是唯一的
```java
/* module-info.java */
module com.example.someModule{

}
```

`com.example.someModule` 表示模块的名称，用包名区分唯一的（非强制）

<h3 id="module-dependency">the module’s dependencies(模块的依赖)</h3>

定义模块依赖了那些模块，未在此定义的模块不能引用（java自带的模块可用 `java --list-modules` 命令查看）

```java
module com.example.someModule{
    // 引用模块
    requires com.example.anotherModule;
}
```

<h3 id="module-exports">the packages it explicitly makes available to other modules(导出模块的包)</h3>

`exports` 定义模块中哪些包可以被其他模块所见（即使用）

`provides` 定义模块提供的服务，服务一般为接口，内部实现不暴露给其他模块

```java
import com.example.somepackage.internal.XXXServiceImpl;

module com.example.someModule{
    // 导出包
    exports com.example.somepackage;
    // 提供服务
    provides com.example.somepackage.XXXService with XXXServiceImpl;
}
```

<h3 id="module-consumer">the services it consumes(消费服务)</h3>

定义模块消费的服务，使用 `use` 定义消费的服务

```java
module com.example.someModule{
    // 引用模块
    requires com.example.anotherModule;
    // 引用服务
    uses com.example.anotherModule.XXXService;
}
```

调用服务的方法

```java
public static void main(String[] args) {
    ServiceLoader<XXXService> xxxLoader = ServiceLoader.load(XXXService.class);
    Optional<XXXService> xxxInstance = xxxLoader.findFirst();
    if (xxxInstance.isPresent()) {
        XXXService xxx = xxxInstance.get();
        xxx.someMethod();
    }
}
```

[JPMS快速入门](http://openjdk.java.net/projects/jigsaw/quick-start)点此
