---
title: React学习笔记 -- 父子组件的通信
date: 2017-06-23 19:21:46
tags:
    - react
    - jsx
    - javascript
    - react类
    - react 生命周期
    - 渲染
    - 组件间的通讯
    - Parent & Children
categories:
    - javascript
    - react
---

在前面的章节中我们学习了如何在组件中管理自身的状态，那么问题来了！在实际的开发环境中不可能只有一个组件，那么组件之间进行通信就很关键了，如何通信？我们今天来学习父子组件间的通信！

在React关于Component[生命周期](https://facebook.github.io/react/docs/react-component.html#the-component-lifecycle)的介绍中，在Updating中有**componentWillReceiveProps**这样一个状态,它的介绍如下
>componentWillReceiveProps() is invoked before a mounted component receives new props. If you need to update the state in response to prop changes (for example, to reset it), you may compare this.props and nextProps and perform state transitions using this.setState() in this method.

大概的意思就是已经处于Mounted状态的组件在收到新的属性（props）之前的时候会调用componentWillReceiveProps这个方法，如果你需要改变状态去响应属性的变化，你可能要比较当前的props和改变的props然后使用在这个方法中使用setState去改变状态。
#
需要注意的是如果shouldComponentUpdate返回了false,componentWillReceiveProps、render和componentDidMount方法将不会执行
#
下面看例子：
### 父组件
```javascript
class Parent extends React.Component{
  constructor(props){
    super(props);
    this.state = {
      parentText:'父组件的初始状态',
      childText:'子组件的初始状态'
    };
    this.handleParentButtonClick = this.handleParentButtonClick.bind(this);
    this.handleChildButtonClick = this.handleChildButtonClick.bind(this);
  }

  /**
   * 父组件的按钮点击,改变子组件label的值
   */
  handleParentButtonClick(){
    this.setState({
      childText:'父组件按钮点击，子组件label值改变'
    });
  }

  /**
   * 子组件的按钮点击，改变父组件label的值
   */
  handleChildButtonClick(){
    this.setState({
      parentText:'子组件按钮点击，父组件label值改变'
    });
  }

  render(){
    return <div>
      <p>
        <label>{this.state.parentText}</label>
        <button onClick={this.handleParentButtonClick}>父组件按钮点击</button>
      </p>
      <Child text={this.state.childText} handleClick={this.handleChildButtonClick}  />
      </div>;
  }
}
```

### 子组件
```javascript
class Child extends React.Component{

  constructor(props){
    super(props);
    this.handleChildClick = this.handleChildClick.bind(this);
  }

  handleChildClick(){
    this.props.handleClick();
  }

  render(){
    return <p>
      <label>{this.props.text}</label>
      <button onClick={this.handleChildClick}>子组件按钮点击</button>
    </p>;
  }
}

Child.propTypes = {
  handleClick:React.PropTypes.func,
  text:React.PropTypes.string
};
```
效果是子组件的按钮点击，父组件的label值改变，父组件的按钮点击，子组件的label改变。这里的关键就是当Child组件的props改变时会重新渲染，子组件的按钮点击实际上调用的是父组件里的方法，即直接改变父组件的状态。
#
演示地址：[http://codepen.io/lisijie/pen/KNjLBj](http://codepen.io/lisijie/pen/KNjLBj)
