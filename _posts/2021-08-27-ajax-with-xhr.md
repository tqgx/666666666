---
layout: post
title: "XMLHttpRequest与Ajax"
subtitle: "JS, Web"
date: 2020-11-22
author: "NKQ"
header-img: "img/home-bg-art.jpg"
tags:
  - Web
---


> 浏览学习了“[ruoyiqing的你真的会使用XMLHttpRequest吗？](https://segmentfault.com/a/1190000004322487)”这篇文章

## AJAX和XMLHttpReques

AJAX是基于XMLHttpReques、CSS、JS、HTML使网页更动态的功能实现，可以实现局部的网页动态更新而不必刷新网页，由此可以提高用户体验

## XMLHttpReques LV2

#### 功能更新

[阮一峰XMLHttpRequest Level 2 使用指南](http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html)，写的非常的全面

#### 支持范围

部分浏览器不支持XMLHttpReques或者XMLHttpReques的部分功能，具体的支持范围可以查看这个[网站](https://caniuse.com/?search=XMLHttpRequest)

使用时需要加以判断，代码如下

```javascript
function createXHR() {
    if(window.XMLHttpRequest)
    {
        var xhr = new XMLHttpRequest();                       // 支持 XMLHttpRequest
    }
    else
    {
        var xhr = new ActiveXObject("Microsoft.XMLHTTP");     // 不支持XMLHttpRequest
    }
    return xhr;
};
```

#### 设置请求头

可以使用`setRequestHeader`来设置请求头

```javascript
XHR.setRequestHeader(DOMString header, DOMString value);
```

此方法需要在`open()`方法调用之后，在`send()`方法之前设置
重复调用`setRequestHeader`会将值追加，而不是覆盖

#### 获取响应头

可以使用`getAllResponseHeaders`和`getResponseHeader`两个方法来获取，其中`getResponseHeader`需要传参数

#### 传输的状态

请求的状态有五种，使用0-4来表示，每一次请求的状态变化均会触发`xhr.onreadystatechange`事件
|状态|含义|
|---|---|
|0|未打开，调用`open()`方法改变此状态|
|1|打开但还未发送，已调用`open()`方法但还未调用`send()`方法|
|2|已经获取到请求头，已调用`send()`方法，且请求已发送|
|3|下载响应体，请求正在传输中|
|4|请求已经完成|

#### 传输的数据类型

传输的数据类型可以通过`responseType`来设置

- `''` ---> 字符串(默认值)
- `'text'` ---> 字符串
- `'document'` ---> `document`对象
- `'json'` ---> `JavaScript`对象
- `'blob'` ---> `blob`对象
- `'arrayBuffer'` ---> `arrayBuffer`对象

#### 获取返回的数据

可以使用`xhr.request`、`xhr.requestTEXT`、`xhr.requestXML`来获取`request`的数据
他们有不同的特点，但要获取到正确的值，均需要等待请求完成

|`xhr.request`|`xhr.requestTEXT`|`xhr.requestXML`|
|---|---|---|
|默认值为空字符串|默认值为空字符串|默认值为`null`|
|对格式无要求|需要`responseType`为`'text'`或者`''`|需要`responseType`为`'text'`、`''`或者`document`|
|请求失败时得到的数据依`responseType`而定|请求失败得到空字符串|请求失败得到`null`|

要获取到正确的值，需要加以判断，待请求状态为`4`时再行获取

#### 设置请求的超时时间

可以使用`timeout`方法来设置请求的超时时间，时间由调用`send()`方法开始计算，待计时结束就无论请求状态如何，都会主动将请求结束
此方法可以在调用`send()`方法后再设置

#### 获取上传/下载的进度

`onprogress`事件每50ms触发一次，可以显示上传/下载的进度
- 上传时触发的是`xhr.upload`对象的`onprogress`事件
- 下载时触发`xhr`对象的`onprogress`事件

#### 请求时发送的数据类型

GET/HEAD请求中，`send()`方法中的参数会被置`null`
`xhr.send()`方法中的参数会影响到请求头中`content-type`的默认值

|`data`类型|默认值|
|---|---|
|`document`类型|`application/xml;charset=UTF-8`|
|`document`类型同时为`HTML Document`类型|`text/html;charset=UTF-8`|
|`DOMString`类型|`text/plain;charset=UTF-8`|
|`FormData`类型|`multipart/form-data; boundary=[]`|

此外如果使用上文中提到的`setRequestHeader`设置了请求头，则会将这些默认值覆盖

调用`send()`方法时如果断网可能会报错<kbd>Uncaught NetworkError: Failed to execute 'send' on 'XMLHttpRequest'</kbd>，应该使用`try-catch`来调用`send()`方法

#### 请求事件

请求事件包含以下八个，其中`xhr.upload`没有`onreadystatechange`事件
- `onloadstart`
- `onprogress`
- `onabort`
- `ontimeout`
- `onerror`
- `onload`
- `onloadend`
- `onreadystatechange`

#### 执行回调函数时判断条件

请求成功时会触发`onreadystatechange`和`onload`事件，因此可以这样设置

```javascript
方法一：
xhr.onload = function () {
  if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
  ....
  }
};
方法二：
xhr.onreadystatechange = function () {
    if(XHR.readyState === 4 && xhr.status >= 200 && xhr.status < 300){
    ....
    }
};
```

## 参考

[ruoyiqing的你真的会使用XMLHttpRequest吗？](https://segmentfault.com/a/1190000004322487)