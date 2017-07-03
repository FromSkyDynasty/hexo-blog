---
title: React学习笔记 -- 按条件渲染页面(conditional-rendering)
date: 2017-06-23 19:20:31
tags:
    - react
    - jsx
    - javascript
    - react类
    - react 生命周期
    - 渲染
    - 按条件渲染
categories:
    - javascript
    - react
---

按条件渲染即指根据不同的条件显示不同的内容,就像下面的例子:
<!-- more -->
```javascript
var userGreet = <h1>Welcome back!</h1>;
var guestGreet = <h1>Please login First.</h1>;

function Greet(props) {
    const isLogin = props.isLogin;
    if (isLogin) {
        return userGreet;
    } else {
        return guestGreet;
    }
}

ReactDOM.render(
        <Greet isLogin={false}/>,
        document.getElementById('demo7')
);
```
当isLogin为true的时候显示"Welcome back",false时显示"Plese Login First",下面的例子通过点击登录、登出按钮模拟不同的isLogin状态下显示的内容

```javascript
function LoginButton(props) {
    return (
            <button onClick={props.onClick}>登录</button>
    );
}

function LogoutButton(props) {
    return (
            <button onClick={props.onClick}>登出</button>
    );
}
class LoginControl extends React.Component {
    constructor(props) {
        super(props);
        this.loginClick = this.loginClick.bind(this);
        this.logoutClick = this.logoutClick.bind(this);
        this.state = {isLogin: false};
    }

    loginClick() {
        this.setState({
            isLogin: true
        });
    }

    logoutClick() {
        this.setState({
            isLogin: false
        });
    }

    render() {
        const isLogin = this.state.isLogin;
        let button = null;
        if (isLogin) {
            button = <LogoutButton onClick={this.logoutClick}/>
        } else {
            button = <LoginButton onClick={this.loginClick}/>
        }

        return <div>
            <Greet isLogin={isLogin}/>
            {button}
        </div>
    }
}
```
在render方法中首先获取当前的登录状态，根据isLogin输出不同文字和不同的按钮。

![](http://og1q3elcx.bkt.clouddn.com/react/demo7/should-login.png)
![](http://og1q3elcx.bkt.clouddn.com/react/demo7/logout.png)

在判断的时候可以使用JSX的逻辑运算符**&&**，类似isLogin&&doSomething(),也可使用条件表达式：
```javascript
return {isLogin?(<LoginButton onClick={this.loginClick}/>):(<LoginButton onClick={this.loginClick}/>)};
```

### 通过条件阻止组件的渲染
```javascript
function WarningBanner(props) {
    if (!props.warning) {
        return null;
    }
    return <h2>Warning!!!</h2>;
}

class Page extends React.Component {
    constructor(props) {
        super(props);
        this.showWarning = this.showWarning.bind(this);
        this.state = {
            isShow: false
        };
    }

    showWarning() {
        this.setState(prev=> ({
            isShow:!prev.isShow
        }));
    }

    render() {
        return (<div>
                    <WarningBanner warning={this.state.isShow}/>
                    <button onClick={this.showWarning}>
                        {this.state.isShow ? "hide" : "show"}
                    </button>
                </div>
        );
    }
}

ReactDOM.render(
        <Page />,
        document.getElementById('demo7')
);
```
通过点击show/hide按钮显示和隐藏WarningBanner
