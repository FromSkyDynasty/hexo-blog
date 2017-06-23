---
title: React学习 - JSX语法(js与html混写)
date: 2017-06-23 19:12:34
tags:
    - react
    - jsx
    - javascript
categories:
    - javascript
    - react
---

在React中,script的type属性为text/babel,与之前写的text/javascript不同,这个就是JSX,下面看代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>React第二弹</title>
    <script type="text/javascript" src="../react.js"></script>
    <script type="text/javascript" src="../react-dom.js"></script>
    <script type="text/javascript" src="../browser.min.js"></script>
</head>
<body>
<div id="demo2"></div>
<script type="text/babel">
    /** example1 **/
    var names = ['Alice', "Tom", "Lily"];
    ReactDOM.render(
            <div>
                {
                    names.map(function (item) {
                        // 1.return "name:" + item;
                        return <h1>name:{item}</h1>
                    })
                }
            </div>,
            document.getElementById('demo2')
    );
    /** example2 **/
    var doms = [
            <h1>name:Alice</h1>,
            <h1>name:Tom</h1>,
            <h1>name:Lily</h1>
    ];
    ReactDOM.render(
            <div>{doms}</div>,
            document.getElementById('demo2')
    );

    /** example3 **/
    var json = {
        "demo1":<h1>Hello world!</h1>,
        "demo2":<h1>JSX语法</h1>
    };

    ReactDOM.render(
            {json},
            document.getElementById('demo2')
    );
</script>
</body>
</html>
```

### example1 :
例子一中预先定义了普通的JS数组,然后在div块中循环输出,结果如下:

![](http://og1q3elcx.bkt.clouddn.com/reactreact-demo2-result.png)

### example2:
例子二中的数组采用JSX定义,然后通过{doms}调用,react内部循环处理,结果与
example1相同

### example3
例子三定义了一个JSX形式的json数据,仿example2进行输出,结果得到如下错误:

![](http://og1q3elcx.bkt.clouddn.com/reactreact-json-error.png)

错误的大概意思就是"对象不是一个有效的react节点,如果想渲染一个集合,用数组替换或者用createFragment创建"