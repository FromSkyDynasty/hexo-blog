---
title: React学习笔记 -- 属性(props)
date: 2017-06-23 19:15:52
tags:
    - react
    - jsx
    - javascript
    - react类
    - props
categories:
    - javascript
    - react
---

属性作用于组件上,属性名可以为任意字符串,下面看例子

```javascript
var Demo4Class = React.createClass({
    render: function () {
        return <h1>Hello,{this.props.name}</h1>
    }
});

ReactDOM.render(
    <Demo4Class name="Jack"/>,
    document.getElementById("demo4")
);
```
运行结果:
![](http://og1q3elcx.bkt.clouddn.com/react/simple-result.png)

### 特殊props

### 1.children
>children是props中的一个特殊属性,表示组件的子节点,下面是例子:
```javascript
var Demo4Class = React.createClass({
    render: function () {
        return <ul>
            {
                React.Children.map(this.props.children, function (child) {
                    return <li>{child}</li>
                })
            }
        </ul>
    }
});

ReactDOM.render(
        <Demo4Class>
            <span>Hello,Jack!</span>
            <span>Hello,Lily!</span>
            <span>Hello,Tiger!</span>
        </Demo4Class>,
        document.getElementById("demo4")
);
```
运行结果:
![](http://og1q3elcx.bkt.clouddn.com/react/props-children-result.png)
>尝试使用children作为普通属性使用
```javascript
var Demo4Class = React.createClass({
    render: function () {
        return <h1>Hello,{this.props.children}</h1>
    }
});

ReactDOM.render(
    <Demo4Class children="Jack"/>,
    document.getElementById("demo4")
);
```
无异常,且正常运行,但是建议在开发中不要使用children作为普通属性,以免混淆

### 2.js保留关键字class,for
>若作为普通属性,也可正常运行,但也不建议使用这两个甚至于其他html标签的一些属性
