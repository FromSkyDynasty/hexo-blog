---
title: 记一次clinit产生的NPE
date: 2018-06-06 18:22:46
tags:
    - java-clinit
    - java-init
    - npe
categories:
    - java
---

最近在做项目的时候碰到了一个以前未见过的NPE，<!-- more -->代码如下:

```java
public class BookingState implements Serializable {

    private static final long serialVersionUID = 1L;

    public static final BookingState INITIAL = new BookingState("00000");

    private static final Pattern VALUE_REGEXP = Pattern.compile("^-?\\d{5}$");

    public BookingState(String value) {
        if (value == null || !VALUE_REGEXP.matcher(value).matches()) {
            throw new IllegalArgumentException("invalid value \"" + value + "\"");
        }
        // ...
    }

    // ....
}
```

乍一看，没什么问题，可事实就是在程序启动时报了如下错误：

![](https://oi4kggmuk.qnssl.com/images/blog/npe.png)

既然存在`value == null`的判断，就排除了value为空的可能原因，那么我就将注意力放在了`<init>`和`<clinit>`上了，通过查找资料，对于init和clinit的定义如下：

#### clinit

> 类"初始化"阶段，它是一个类或接口被首次使用的前阶段中的最后一项工作，本阶段负责为类变量赋予正确的初始值。

> Java 编译器把所有的类变量初始化语句和类型的静态初始化器通通收集到 `<clinit>` 方法内，该方法只能被 Jvm 调用，专门承担初始化工作。

> 除接口以外，初始化一个类之前必须保证其直接超类已被初始化，并且该初始化过程是由 Jvm 保证线程安全的。另外，并非所有的类都会拥有一个 `<clinit>()` 方法，在以下条件中该类不会拥有 `<clinit>()` 方法：

>
- 该类既没有声明任何类变量，也没有静态初始化语句；
- 该类声明了类变量，但没有明确使用类变量初始化语句或静态初始化语句初始化；
- 该类仅包含静态`final`变量的类变量初始化语句，并且类变量初始化语句是编译时常量表达式。

#### init

> 在类被装载、连接和初始化，这个类就随时都可能使用了。对象实例化和初始化是就是对象生命的起始阶段的活动，在这里我们主要讨论对象的初始化工作的相关特点。

> Java 编译器在编译每个类时都会为该类至少生成一个实例初始化方法--即 `<init>()` 方法。此方法与源代码中的每个构造方法相对应，如果类没有明确地声明任何构造方法，编译器则为该类生成一个默认的无参构造方法，这个默认的构造器仅仅调用父类的无参构造器，与此同时也会生成一个与默认构造方法对应的 `<init>()` 方法.

> 通常来说，`<init>()` 方法内包括的代码内容大概为：调用另一个 `<init>()` 方法；对实例变量初始化；与其对应的构造方法内的代码。

> 如果构造方法是明确地从调用同一个类中的另一个构造方法开始，那它对应的 `<init>()` 方法体内包括的内容为：一个对本类的`<init>()` 方法的调用；对应用构造方法内的所有字节码。

> 如果构造方法不是通过调用自身类的其它构造方法开始，并且该对象不是 Object 对象，那 `<init>()` 法内则包括的内容为：一个对父类 `<init>()` 方法的调用；对实例变量初始化方法的字节码；最后是对应构造子的方法体字节码。

> 如果这个类是`Object`，那么它的`<init>()`方法则不包括对父类`<init>()`方法的调用。

明白上面两个方法的作用后，再次检查我们的代码，就会发现问题了，在初始化`INITIAL`对象时调用了构造方法，而构造方法使用了未初始化的变量`VALUE_REGEXP`(`INITIAL`对象在`VALUE_REGEXP之前定义`)，所以就产生了NPE.

所以要解决该问题就只需要将`VALUE_REGEXP`的定义放到`INITIAL`对象之前。


### 总结

产生这个问题的主要原因是对JVM虚拟机不了解，所以以后要多对JVM和JAVA底层进行了解。

### 参考链接

[java中init()和clinit()方法的区别](https://blog.csdn.net/JAVA528416037/article/details/48463639)

[Java: ExceptionInInitializerError caused by NullPointerException when constructing a Locale object](https://stackoverflow.com/questions/10325744/java-exceptionininitializererror-caused-by-nullpointerexception-when-constructi)

[解析 Java 类和对象的初始化过程](https://www.ibm.com/developerworks/cn/java/j-lo-clobj-init/index.html)