# WEB 安全

## XSS 攻击

恶意攻击者往 Web 页面里插入恶意 script 代码，当用户浏览该页时，嵌入其中 Web 里面的 script 代码会被执行，从而达到恶意攻击用户的目的。

- 反射型：被动的非持久性 XSS，诱导用户点击短型 URL，服务器解析后响应，在返回的响应内容中隐藏和嵌入攻击者的 XSS 代码，从而攻击用户。
- 持久型：也叫存储型 XSS，主动提交恶意数据到服务器，当其他用户请求后，服务器从数据库中查询数据并发给用户受到攻击。
- DOM 型：DOM 通过 html 结构执行事件脚本。

```html
<img src="xx" onerror="console.log(document.cookie);" />
```

## CSRF 攻击

CSRF（Cross-site request forgery），中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。

![csrf](fontend-security-csrf.jpg)

### CSRF 防范

- 低安全级别：被黑客抓包，获取请求地址，直接修改参数。
- 中安全级别：加入验证码，判断 reffer，这些参数前端都是可以篡改的。（流失用户）
- 高安全级别：验证 token，每次动态更新。
- 终极防范： 强验证码 + 动态 token 请求。

### Token

CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于 Cookie 中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的 Cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 Cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。

动态 token：每当服务器端验证过 token 之后，便会生成一个新的 token 返给客户端，这样保证客户端手里的 token 只能使用一次，即使 token 被劫持，被劫持到的 token 也已过期，不能使用了。

## Web Shell 网站提权渗透

它是以 asp，php，jsp 或者 cgi 等网页文件形式存在的一种命令执行环境，由于 webshell 其大多数是以动态脚本的形式出现，也有人称之为网站的后门工具。

- 编写可执行的 web 脚本文件（木马）
- 寻找上传漏洞，将我们的木马上传
- 找到上传后路径，并执行
- 安装好中国菜刀，管理木马（获取整个网站的目录了）

上传文件扩展名

![上传文件扩展名](fontend-security1.png)

上传文件 HTTP 请求伪造

![上传文件 HTTP 请求伪造](fontend-security2.png)

防范：

- 1、上传时检测字节码。如果是图片，字节码前面的几个字符是固定的。
- 2、修改上传文件名。
- 3、修改静态资源的执行权限。

## 网页挂马和流量劫持

### 网页挂马

网页挂马指的是把一个木马程序上传到一个网站里面然后用木马生成器生一个[网马](https://baike.baidu.com/item/%E7%BD%91%E9%A9%AC)，再上到空间里面！再加代码使得木马在打开网页时运行！

### 流量劫持

如果你原本想访问 A 网站，但是有人偷偷做了手脚，让你实际上打开的是 B 网站，这就叫流量劫持。

常见的劫持包括：[DNS 劫持](/blog/dns-cdn.html)，路由器弱口令，WiFi 弱口令等。

---

::: tip 参考资料
[流量劫持](http://fex.baidu.com/blog/2014/04/traffic-hijack/)
:::
