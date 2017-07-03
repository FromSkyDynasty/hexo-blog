---
title: springMVC参数类型转换
date: 2017-06-21 19:16:33
tags:
    - spring
    - springMVC
    - java
    - 类型转换
categories:
    - java
---

最近在使用SpringMVC的时候碰到如下问题，故记录下来

### 问题描述：

在Controller的方法中接受一个BigDecimal的参数，在JS传一个小数过来时，js端收到的返回码为[400(Bad Request)](http://baike.baidu.com/link?url=JZwlb1NADJQ22oGk4bAYCKurRFfMiBQIoJQRz42yOQpb4tpWjnjDXDOZbJVbi-WRPW2r6_m2mCLYGeHCF-G7Cq)
<!-- more -->
### 解决方法：
使用SpringMVC提供的**ServletRequestDataBinder**和**@initBinder**注解
在@InitBinder的注解的源码中有如下注释：

> Annotation that identifies methods which initialize the
 {@link org.springframework.web.bind.WebDataBinder} which
 will be used for populating command and form object arguments of annotated handler methods.

大致的意思是**标识初始化WebDataBinder的方法的注解，其中将用于填充命令和表单对象参数的注释处理程序方法。** ServletRequestDataBinder继承于WebDataBinder，其中**registerCustomEditor**方法用于注册自定义的转换器，转换器的类型为**PropertyEditor**接口，**PropertyEditorSupport**已经实现了该接口，我们只需要复写其中的**setAsText**方法，然后调用**setValue**方法将转换过得值赋给需要的变量。

### 代码如下：

### BigDecimalEditor.java
```java
import org.apache.commons.lang3.StringUtils;

import java.beans.PropertyEditorSupport;
import java.math.BigDecimal;

public class BigDecimalEditor extends PropertyEditorSupport {

    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        if (StringUtils.isBlank(text)) {
            setValue(null);
        } else {
            try {
                double temp = Double.parseDouble(text);
                setValue(BigDecimal.valueOf(temp));
            } catch (Exception e) {
                throw new IllegalArgumentException();
            }
        }
    }
}
```

### XXXController.java
```java
@Controller
public class XXXController{

    @RequestMapping(value="/path")
    public void someMethod(BigDecimal someVar){
        System.out.println(someVar);
    }

    @InitBinder
    public void initWebBinder(ServletRequestDataBinder binder) {
        binder.registerCustomEditor(BigDecimal.class, new BigDecimalEditor());
    }
}
```

如需要转换其他类型，与上述方法类似
