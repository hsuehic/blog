---
title: Express应用生产环境最佳实践： 安全
date: 2018-05-12 14:10:45
tags:
- node.js
- security
- TLS
categories:
- node.js
- express
thumbnail: /images/nodejs.png
barnar:
---

## 概览

术语“production”指软件处于生命周期中应用和服务对于终端用户和消费者可用的阶段。 相反的, 在“development”阶段， 还有编写和测试代码，应用还没有对外部开放。 相对的， 相应的系统环境也称作"production" 和 “development”环境。
开发环境和生产环境往往在设置上有很大的差异，需求也有很大的不同。 可能在开发环境中很好的设置，在生产环境中就不可用了。 例如，在开发环境中你需要错误用志用来调试，但在生产环境中这可能是一个安全漏洞。 或许在开发环境中你不需要担心伸缩性部署、可靠性和性能，但在生产环境这有严格的要求。

<!-- more -->

在生产环境中安全的最佳实践包括以下方面。
- [不使用过时的或有安全漏洞的Express版本](#version)
- [使用TSL](#tsl)
- [使用Helmet](#helmet)
- [安全的使用cookies](#cookies)
- [确保依赖资源是安全的](#dependencies)
- [防止其它已知的安全漏洞](#know-vulnerabilities)
- [更多可以考虑的方面](#more)

<h2 id="version">不使用过时的或有安全漏洞的Express版本</h2>

Express 2.x 和 3.x已经不在维护。 这些版本的安全问题将不会解决。 不要再使用这些版本。 如果还没有迁移到 版本 4，请参考 [迁移向导](https://expressjs.com/en/guide/migrating-4.html)。

同时确保你没有使用有安全风险的Express版本， 参考[安全更新](https://expressjs.com/en/advanced/security-updates.html)页面。 如果有， 请更新到稳定版本，最好是最新的稳定版本。

<h2 id="tsl">使用TSL</h2>
如果你的应用需要处理或传输敏感的数据， 使用[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)(TLS)来保证数据连接安全。 这项技术在数据从客户端发送到服务器前对数据进行加密，这样可以防止一些常见的（容易）的攻击。 虽然 Ajax和Post请求不是明显示可见， 看起来隐藏在浏览器后面， 但网络传输存在着被[抓包](https://en.wikipedia.org/wiki/Packet_analyzer)和[中间层攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)的风险。

你可能很熟悉Secure Socket Layer(SSL)加密。[TLS是SSL的新一阶段的产品](https://msdn.microsoft.com/en-us/library/windows/desktop/aa380515(v=vs.85).aspx)。也就是说，如果你已经在使用SSL， 可以考虑升级到TLS。 通常，建议使用Nginx来处理TLS。 在Nginx中配置TLS,请参考 [Recommended Server Configurations (Mozilla Wiki)](https://wiki.mozilla.org/Security/Server_Side_TLS#Recommended_Server_Configurations)

另外，[Let’s Encrypt](https://letsencrypt.org/about/) 一个方便的获取免费的TLS证书的工具, 它是由[Internet Security Research Group (ISRG)](https://letsencrypt.org/isrg/)提供的开方的CA工具。

<h2 id="helmet">使用Helmet</h2>
[Helmet](https://www.npmjs.com/package/helmet)可以通过合适的设置http协议头帮助保护应用远离一些常见的Web安全隐患。

Helmet 确切的说就是9个小的设置安全相关的http协议头的中间件函数的集合：

- [CSP](https://github.com/helmetjs/csp) 设置 Content-Security-Policy 头 帮助防止跨站脚本攻击和跨站脚本注入。
- [HidePoweredBy](https://github.com/helmetjs/hide-powered-by) 移除X-Powered-By 头。
- [hpkp](https://github.com/helmetjs/hpkp) 添加[Public Key Pinning](https://developer.mozilla.org/en-US/docs/Web/Security/Public_Key_Pinning) 头，防止通过伪造证书书的man-in-the-middle攻击。
- [hsts](https://github.com/helmetjs/hsts) 设置Strict-Transport-Security 头加强（HTTP Over SSL/TLS）到服务器的连接。
- [ieNoOpen](https://github.com/helmetjs/ienoopen) 设置X-Download-Operation for IE8+。
- [noCache](https://github.com/helmetjs/nocache) 设置Cache-Control和[Pragma](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Pragma)头。
- [noSniff](https://github.com/helmetjs/dont-sniff-mimetype) 设置X-Content-Type-Options 以防止浏览器被MIME-Sniffing通过已经定义的content-type。
- [xssFilter](https://github.com/helmetjs/x-xss-protection) 设置X-XSS-Protection 以在大多数现代浏览器中启用跨站脚本过滤。

安装Helmet和其它模块一样：
```bash
$ npm install --save helmet
```

然后在代码中使用：
```javascript
// ...

var helmet = require('helmet')
app.use(helmet())

// ...
```

### 至少移除 X-Powered-By 头

如果你不想使用 Helmet, 至少移除X-Powered-By 头。 攻击者可以使用这个头（通常被默认开启） 来探测Express应用，并执行特定目标的攻击。

因此，最好的方法是使用app.disable()方法关闭这个头:
```javascript
app.disable('x-powered-by')
```

如果你使用Helmet, 它将帮你做这些。

<h2 id="cookies">安全的使用cookies</h2>
确保cookies不会停你的用应暴露给其它方，不要使用默认的cookie名称，合理的设置cookie安全选项。

主要有两个cookie会话相关的中间件模块。

- [express-session](https://www.npmjs.com/package/express-session) 替换 Express 3.x内置的express.session中间件。
- [cookie-session](https://www.npmjs.com/package/cookie-session) 替换 Express 3.x 内容的express.cookieSession.

这两个模块的主要区别是如何保存cookes会话信息。 express-session模块在服务器端存储会话信息， 它仅仅在cookie中保存session ID， 不舍保存会话数据。 默认下， 它使用内存存储， 这不适合在生产环境中使用。 在生产环境中，需要创建一个可伸缩的会话存储，查看[兼容的会话存储]列表(https://github.com/expressjs/session#compatible-session-stores)。
相反的， cookie-session 中间件实现了基于cookie的存储： 它将整个会话序列化成cookie， 而不仅仅是会话key。只有在传话数据相当小且很容易具很容易编码成基本数据值的情况下使用。 浏览器支持至少4096字节大小的cookie，确保不会超出这个限制， 每个域名下的cookie大小不要超过4093字节。 另外，需要考虑cookie数据是在客户端可见的， 在考虑安全和隐私的情况下 express-session会是更好的选择。

### 不要使用默认的会话cookie名称

使用默认的会话cookie名称会使你的应用容易被攻击。 这个问题暴露类似于X-Powered-By的风险： 潜在的攻击者可以使用这个特点有针对的进行攻击。

避免这个问题，使用通用的cookie名称; 例如, 使用 express-session 中间件：

```javascript
var session = require('express-session')
app.set('trust proxy', 1) // trust first proxy
app.use(session({
  secret: 's3Cur3',
  name: 'sessionId'
}))
```

### 设置Cookie 安全选项

设置以下cookie选项加强安全：
- secure - 确认浏览器使用https情况下传输cookie。
- httpOnly - 确认cookie只在http(s)中传输，不可以在客户端JavaScript更改， 以防止跨站脚本攻击。
- path - 指明cookie的路径， 使用它来对比请求路径。 在路径和域一致的情况下才发送cookie。
- expires - 为持久化的cookie设置过期时间。

下面是一个使用cookie-session中间件的示例：
```javascript 
var session = require('cookie-session')
var express = require('express')
var app = express()

var expiryDate = new Date(Date.now() + 60 * 60 * 1000) // 1 hour
app.use(session({
  name: 'session',
  keys: ['key1', 'key2'],
  cookie: {
    secure: true,
    httpOnly: true,
    domain: 'example.com',
    path: 'foo/bar',
    expires: expiryDate
  }
}))
```

<h2 id="dependencies">确保依赖资源是安全的</h2>

使用npm来管理依赖，功能很强大，也很方便。 但是使用这些包也可能带来致命的安全隐患。 应用的安全将和你链接到你应用中的最弱的依赖一样。

使用以下两个工具任一或全部来确保第三方包的安全： [nsp](https://www.npmjs.com/package/nsp) 和 [Snyk](https://snyk.io/)。

 nsp 是一个检测[Node安全项目](https://nodesecurity.io/)隐患数据库来判断应用是否使用了带有已经安全隐患的第三方包。

 安装方式如下：
 ```bash
 $ npm i nsp -g
 ```

 使用以下命令来提交npm-shringkwarp.json/package.json文件到[https://nodesecurity.io/](https://nodesecurity.io/)验证：
 ```bash
 $ nsp check
 ```

 Snyk 提供了命令行工具，也提供了图形界对比[Snyk’s open source vulnerability database](https://snyk.io/vuln/)检测应用的第三方依赖是否存在安全风险。
 安装Cli工具：
 ```bash
 $ npm i -g snyk
 $ cd your-app
 ```
 使用命令行检测安全风险
 ```bash
 $ snyk test
 ```
使用以下命令行打开一个引导更新和修复安全风险的向导：
```bash

$ snyk wizard
```
<h2 id="know-vulnerabilities">防止其它已知的安全漏洞</h2>
密切关注 Node Security Project 和 Snyk关于Express或你应用使用到的其它模块的建议。 另外，这些数据库也是Node安全方面的优秀的资源和工具。

最后， Express应用和其它Web应用一样，面临着各种各样的Web攻击。学习和了解这些Web安全风险，并小心避免它们。

<h2 id="more">更多可以考虑的方面</h2>

下面还有一些来自于[Node.js Security Checklist](https://blog.risingstack.com/node-js-security-checklist/)更进一点的建议。 参考上述博客获取更详细的信息：
- 实施速率限制避免对身份验证的暴力攻击。 一个可行的方法是使用[StrongLoop API Gateway](https://strongloop.com/node-js/api-gateway/)来增加频率限制策略。或者， 你可以使用[express-limiter](https://www.npmjs.com/package/express-limiter)， 但是这样要求你修改特定的代码。
- 使用 [csurf](https://www.npmjs.com/package/csurf)中间件防止跨站请求伪造攻击。
- 总是过滤和检测用户输入，防止跨站脚本攻击和命令注入。
- 使用参数化和预定义表达式（存储过程）来防止SQL注入风险。
- 使用开源的 [sqlmap](http://sqlmap.org/)工具来检测应用中SQL注入风险。
- 使用[nmap](https://nmap.org/) 和[sslyze](https://github.com/nabla-c0d3/sslyze)工具来检测SSL密码,密钥，以及证书的有效性。
- 使用[safe-regex](https://www.npmjs.com/package/safe-regex)来确保正则表达式不容易被[regular expression denial of service](https://www.owasp.org/index.php/Regular_expression_Denial_of_Service_-_ReDoS)攻击。