---
title: javascript使用正则表达式获取路径中的参数
date: 2017-06-23 19:00:12
tags:
    - javascript
    - 正则表达式
    - 获取url的参数
categories:
    - javascript
---

路径中的参数格式为key1=value1&key2=value2...使用?跟在请求路径上,那么获取参数就可以这样:
<!-- more -->
```javascript
function(url){
    url = url.split('?')[1];
    var pairs = url.split('&');
    for(var i=0;i<pairs.length;i++){
        var pair = pairs[i];
        var values = pair.split('=');
        console.log('key='+values[0] + ',value='+values[1]);
    }
}
```
但是不仅用起来麻烦,而且处理起特殊的url有误,使用正则表达式则会避免这些

### 获取单个参数
```javascript
/**
 * @param name 要取参数的key
 */
function getQueryString(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    var r = window.location.search.substr(1).match(reg);
    if (r != null) return unescape(r[2]); return null;
}
```

### 获取全部参数

```
var params = {};
function getUrlParams(url) {
    var queryRegExp = new RegExp('([\\?|&])(.+?)=([^&?]*)', 'ig');
    var result = queryRegExp.exec(url);
    while (result) {
        console.log(result);
        params[result[2]] = result[3];
        result = queryRegExp.exec(url);
    }
}
```
在queryRegExp中'ig'的含义为不区分大小写,匹配全部
为什么选取result[2]和result[3],看result在控制台中的结果:

![](http://og1q3elcx.bkt.clouddn.com/javacript/js-get-url-params.png)

参考链接:
1.  [http://www.netingcn.com/url-get-parameter-value.html](http://www.netingcn.com/url-get-parameter-value.html)
2.  [https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)