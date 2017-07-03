---
title: React学习 -- 类(Class)
date: 2017-06-23 19:15:52
tags:
    - react
    - jsx
    - javascript
    - react类
categories:
    - javascript
    - react
---

### 类,即组件(component),可以重复使用的部分
React使用createClass来创建类,下面是示例
<!-- more -->
### step1:创建空的类
```javascript
var firstClass = React.createClass({

});
```
在浏览器中运行将会报如下错误:
![](http://og1q3elcx.bkt.clouddn.com/react/react_demo3_render_error.png)
意思就是创建类必须实现render方法

### step2:实现render方法

```javascript
var firstClass = React.createClass({
    render: function () {
        return <h1>This is first components!</h1>;
    }
});
```
运行正常

### step3:使用组件

```javascript
var firstClass = React.createClass({
    render: function () {
        return <h1>This is first components!</h1>;
    }
});

ReactDOM.render(
    <firstClass/>,
    document.getElementById('demo3')
);
```
运行后未报错,也没有显示预期的结果

### step4:将firstClass改为FirstClass

```javascript
var FirstClass = React.createClass({
    render: function () {
        return <h1>This is first components!</h1>;
    }
});

ReactDOM.render(
     <FirstClass />,
     document.getElementById('demo3')
);
```
运行成功,下面是运行结果:

![](http://og1q3elcx.bkt.clouddn.com/react/react_demo3_success.png)

原因:React 的 JSX 里约定分别使用首字母大、小写来区分本地组件的类和 HTML 标签。render渲染时，会把大写的组件名定义为自定义组件，把小写的组件名定义为HTML自带的标签名进行渲染。


