---
title: React学习笔记 -- 状态和生命周期(State and Lifecycle)
date: 2017-06-23 19:15:52
tags:
    - react
    - jsx
    - javascript
    - react类
    - react 生命周期
    - 组件
categories:
    - javascript
    - react
---

### 尝试使用前面所学的知识去更新UI,大致代码如下:
<!-- more -->
```javascript
function showNow() {
    const element = (
        <div>
           现在时间是:{new Date().toLocaleTimeString()}
        </div>
    );

    ReactDOM.render(
        element,
        document.getElementById('demo5')
    );
}

setInterval(showNow, 1000);
```
那么每隔一秒就会去刷新当前的时间

### 如果我们要让其自动更新时间,而不必去调用setInterval来循环刷新,就用到了state
>state与props类似,但state是组件私有的,由组件全权控制

要实现自动更新,就要将showNow由函数改为组件,并且这个组件继承与React.Component,而且这个类要**实现render方法**,**拥有一个构造器**.(是不是与java的类很相似呢!!)
```javascript
class Clock extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            date: new Date()
        };
    }

    render() {
        return (<div>
            现在时间是:{this.state.date.toLocaleTimeString()}
        </div>)
    }
}

ReactDOM.render(
    <Clock/>,
    document.getElementById('demo5')
);
```
运行后,显示了当前时间,但并没有更新时间,原因是只是给了状态,并没有实现定时更新,也就是下面要说的生命周期:
>**componentDidMount**表示组件已经输出到DOM
>**componentWillUnmount**表示组件任务完成,即将解除"安装"

据此,在componentDidMount中启动定时器,然后在componentWillUnmount中结束定时器,将Clock改为:
```javascript
class Clock extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            date: new Date()
        };
    }

    render() {
        return (<div>
            现在时间是:{this.state.date.toLocaleTimeString()}
        </div>)
    }

    showNow() {
        this.setState({
            date: new Date()
        });
    }

    componentDidMount() {
        this.timer = setInterval(
                ()=>this.showNow(),
        1000);
    }

    componentWillUnmount() {
        clearInterval(this.timer);
    }
}
```
运行结果:每隔一秒就会去刷新当前的时间

在[官方文档](https://facebook.github.io/react/docs/state-and-lifecycle.html)中需要注意一个地方:

不要直接去使用this.state.date=something去直接更新state,因为this.props和this.state可能是异步更新的，你不应该依赖它们的值来计算下一个状态。应该使用**setState()**去更新状态

### 这些内容很复杂,还得花些时间去消化!!!

