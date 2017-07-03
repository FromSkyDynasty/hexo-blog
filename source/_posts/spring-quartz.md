---
title: Spring使用quartz配置定时器
date: 2017-06-21 18:27:48
tags:
    - spring
    - quartz
    - 定时器
categories:
    - java
---

[quartz](http://www.quartz-scheduler.org/)是一个强大的开源作业调度框架，支持分布式，这篇文章教会大家如何在spring中配置定时器（分布式配置请移步[http://www.tuicool.com/articles/B3qeUrB](http://www.tuicool.com/articles/B3qeUrB)）。
<!-- more -->
### maven配置

引入quartz的依赖
```xml
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.2.1</version>
</dependency>
```

### Spring中的配置

```xml
<!-- 任务bean -->
<bean id="taskObj" class="com.example.TaskObj"/>

<!-- 任务 -->
<bean id="jobDetail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
    <property name="targetObject" ref="taskObj"/>
    <property name="targetMethod" value="taskMethod"/>
</bean>

<!-- 任务触发器 -->
<bean id="taskTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
    <property name="jobDetail" ref="jobDetail"/>
    <property name="startDelay" value="0"/>
    <property name="cronExpression" value="0 0 0/1 * * ?"/>
</bean>

<!-- 任务调度工厂 -->
<bean id="timerFactory" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
    <property name="triggers">
        <list>
            <ref bean="taskTrigger"/>
        </list>
    </property>
</bean>
```

配置说明

taskObj:    任务所在的实体
#
jobDetail： 交由quartz管理的任务

-- targetObject:参见taskObj
-- targetMethod:定时器启动时执行的方法
#

taskTrigger：任务触发器

-- jobDetail:任务
-- startDelay：延时多少时间执行，默认为0
-- cronExpression：任务调度的时间表达式，关于这个表达式的格式可参见[http://www.cnblogs.com/yaowen/p/3779284.html](http://www.cnblogs.com/yaowen/p/3779284.html),若是不理解可以偷懒：[在线生成cronExpression](http://cron.qqe2.com/)。(建议理解后自己写)

timerFactory：任务调度工厂

配置完成，赶快去试下效果吧

