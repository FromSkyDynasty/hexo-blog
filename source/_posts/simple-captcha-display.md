---
title: 生成简单的图形验证码（下）--  在页面中展示验证码
date: 2017-06-21 19:23:34
tags:
    - javascript
    - ajax
    - blob
    - xhr
    - XMLHttpRequest
    - 验证码
categories:
    - java & javascript
---

在上一篇文章[生成简单的图形验证码（上）](simple-captcha.html)中已经知道如何生成验证码，这节中我们来学习如何在HTML页面上展示验证码。
话不多说，撸代码：
<!-- more -->
首先尝试使用jquery的ajax:
```javascript
$.ajax({
    url:'http://localhost:8080/captcha',
    type:'get',
    success:function(img){
        console.log(img);
    }
});
```

控制台将会打印一串乱码，因为ajax并没有解析img的dataType(大家可以尝试换几种dataType来请求)

### 解决方案

#### 方案一：

既然在response中设置了返回的类型为*image/jpeg*，那么将请求验证码的链接作为img的src肯定是没有问题的

```
<img src="http://localhost:8080/captcha" />
```

成功输出验证码，那么问题来了:浏览器本身是会缓存图片的，img的使用相同的src很大概率会被缓存下来，解决办法就是在链接后面加上随机的参数，如下所示：

```html
<img src="http://localhost:8080/captcha" id="captcha" />
<button onclick="getCaptcha()">看不清，换一张</button>
<script>
    function getCaptcha(){
        document.getElementById('captcha').src =   'http://localhost:8080/captcha?ver='+new Date().getTime();
    }
</script>
```

#### 方案二：

使用XMLHttpRequest,使用这个请求将responseType设置为blob(即返回类型为文件)，然后获取Blob的url,赋值给img
```javascript
function getCaptcha(){
    XMLHttpRequest xhr = new XMLHttpRequest();
    xhr.open('get', 'http://localhost:8080/captcha', true);
    xhr.responseType = 'blob';
    xhr.onload = function () {
        if (this.status === 200) {
            var resp = this.response;
           document.getElementById('captcha').src = window.URL.createObjectURL(resp);
        }
    };
}
```

### 参考文章

[ajax请求并处理二进制流(图片)](https://segmentfault.com/q/1010000000443286)

[理解DOMString、Document、FormData、Blob、File、ArrayBuffer数据类型](http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/)

[java web项目生成验证码的解决方案](http://blog.csdn.net/chenghui0317/article/details/12526439)
