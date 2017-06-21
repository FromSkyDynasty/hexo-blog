---
title: 解决：SpringMVC使用拦截器后ajax无法获取返回的数据
date: 2017-06-21 18:18:24
tags:
    - spring
    - ResponseBody
    - spring拦截器
categories:
    - java
---

最近在使用SpringMVC时碰到如标题所说的问题，具体原因还未找到，解决办法是采用动态代理的方法（AspectJ），就是常说的AOP，思路来自于[http://www.iteye.com/problems/87048](http://www.iteye.com/problems/87048)中的第一个答案。

解决方法：将拦截器配置为一个动态代理的类,代理@ResponseBody注解的控制器中的方法

### 拦截器
```java
/*AspectJ动态代理的注解*/
@Aspect
public class SomeInterceptor implements HandlerInterceptor{

    @Override
    public boolean preHandle(HttpServletRequest request,HttpServletResponse response,Object handler) throws Exception{
        // 验证
        return true;
    }

    // ... 其他实现的方法，这里暂时不需要

    // 设置切面
    @Pointcut(value = "@annotation(org.springframework.web.bind.annotation.ResponseBody)")
    public void pointCut() {
    }

    // 引用配置的切面，注意要有返回值
    @Around(value = "pointCut()")
    public Object doAround(ProceedingJoinPoint point) {
        Object result;
        try {
            result = point.proceed();
        } catch (Throwable e) {
            e.printStackTrace();
            // 异常处理，也可返回result
            result = ....
        }
        return result;
    }
}
```

### Spring配置

```xml
<!-- 开启自动代理 -->
<aop:aspectj-autoproxy proxy-target-class="true" />
<bean id="aspect" class="上面的拦截器的类" />
```

注意：**若Spring主配置文件和SpringMVC的配置文件不在一起，则两个文件都需要配置aspectj的自动代理**


