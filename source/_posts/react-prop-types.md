---
title: React学习笔记 -- 属性(props)
date: 2017-06-23 19:15:52
tags:
    - react
    - jsx
    - javascript
    - react类
    - propTypes
categories:
    - javascript
    - react
---

## PropTypes是用于验证props的属性类型
<!-- more -->
```javascript
var Demo5Class = React.createClass({
    propTypes: {
        name: React.PropTypes.string.isRequired
    },
    render: function () {
        return <h1>{this.props.name}</h1>
    }
});

var num = "123";

ReactDOM.render(
    <Demo5Class name={num}/>,
    document.getElementById("demo5")
);
```
----------

将num改为数字再试
```javascript
var num = 123;
```
也会正常运行,但在控制台上将会显示如下警告:
![](http://og1q3elcx.bkt.clouddn.com/react/type_error_warning.png)

设置默认的属性值

```javascript
var Demo5Class = React.createClass({
    propTypes: {
        name: React.PropTypes.string.isRequired
    },
    render: function () {
        return <h1>{this.props.name}</h1>
    },
    getDefaultProps: function () {
        return {
            name: "My name is Default"
        }
    }
});

ReactDOM.render(
    <Demo5Class />,
    document.getElementById("demo5")
);
```
