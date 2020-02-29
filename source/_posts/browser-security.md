---
title: 浏览器安全
date: 2017-04-03 08:16:31
tags: 安全
---

#### 同源策略

同源策略：限制来自不同 “document” 或脚本，互相读取或修改。

区别：不同子域名、端口、协议, 不同源

&lt;script&gt; &lt;img&gt; &lt;iframe&gt; &lt;link&gt; 等标签可以跨域请求，不受同源限制

- 实际上是由浏览器发起一次 GET 请求

- 通过 src 加载的资源，被浏览器限制，使其不能读、写返回的内容

XMLHttpRequest 受同源影响, 可根据返回 HTTP 头确定是否允许跨域

- Access-Control-Allow-Origin

- 理由：js 不能控制 HTTP 头

Flash 主要通过网站提供的 crossdomain.xml 文件判断是否允许跨域

  - MIME 检查确认 crossdomain.xml 是否合法；比如查看 HTTP 头部 Content-Type 是否是 text/*、application/xml、application/xhtml+xml

IE8 CSS 跨域漏洞，@import 把 html 当成 CSS 加载导致

a.html

```html
<html>
<head>
</head>
<body>
    {}body{font-family:
    aaaaaaaaaaaaaaaaa
    bbbbbbbbbbbbbbbbbb
</body>
</html>
```

b.html 的代码

```html
<html>
<head>
<style>
@import url("C:\Users\admin\Desktop\a.html");
</style>
</head>
<body>
<script>
    console.log("test");
        setTimeout(function() {
            var t = document.body.currentStyle.fontFamily;
            alert(t);
        },200);
</script>
</body>
</html>
```

#### 浏览器沙箱

    chrome 架构：
    
        OS-level Sandbox
                ^
                |
        OS/runtime exploit barrier
                ^
                |
        javaScript sandbox
                ^
                |
        web content(untrusted)

sandbox: 限制不可信代码在一定环境，隔离外部资源；如果一定要跨越 sandbox 边界交换数据，则只能通过 API

#### 恶意网址拦截

原理：周期性从服务器获取一份恶意黑名单

“挂马”会在一个正常的网页中通过 &lt;script&gt; 或者 &lt;iframe&gt; 等标签加载一个恶意网址

#### 高速发展的浏览器安全

CSP(Content Security Ploicy)：服务器返回一个 HTTP 头，并在其中描述应该遵守的安全策略

使用 CSP 的方法，插入一个 HTTP 返回头：

```html
X-Content-Sevurity-Policy: policy
```

其中 policy 的描述及其灵活，比如：

```html
X-Content-Sevurity-Policy: allow 'self' *.mydomain.com
```

CSP 的设计理念无疑是出色的，但是配置复杂，导致未很好的得到推广

除了这些安全功能外，浏览器的用户体验越来越好，随之而来导致一些安全隐患

- IE 和 Chrome 会将 "\" 解析为 "/"；而 firefox 不会

- IE、Chrome、firefox 会将 www.google.com?abc 解析为 www.google.com/?abc

- firefox 还可以识别
  - [http://www.cnn.com]
  - [http://]www.cnn.com
  - [http://www].cnn.com
