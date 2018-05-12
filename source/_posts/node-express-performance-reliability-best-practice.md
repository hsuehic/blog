---
title: Express应用生产环境最佳实践：性能和可靠性
date: 2018-05-12 14:20:42
tags:
- performance
- reliability
categories:
- node.js
- express
- porformance and reliability
thumbnail: /images/nodejs.png
barnar:
---

## 概览 

文章讨论Express应用的在生产环境的性能和可靠性的最佳实践。

这个话题很容易的联想到“devops”的世界， 包括传统的开发和运维。 自然的，下面的内容也分成两大块。
<!-- more -->
- 在代码开发中应该做的事情
  - [使用gzip压缩](#gzip)
  - [不要使用同步函数](#forbidden-synchronous-function)
  - [不要直接日志](#forbidden-directly-logging)
  - [正确的处理异常](#properly-handle-exception)
- 环境和部署中要做的事情
  - [NODE_ENV设置了"production"](#env-production)
  - [确保应用能自动重启](#auto-restart)
  - [以集群的方式运行应用](#run-in-cluster)
  - [缓存请求结果](#cache-request-result)
  - [使用负载均衡](#use-load-balance)
  - [使用反向代理](#use-reverse-proxy)

## 在代码开发中应该做的事情

<h3 id="gzip">使用gzip压缩</h3>
Gzip压缩可以很大程度的减小返回响应body的大小，加快web应用的速度 。 在Express应用中使用压缩中间件。如下示例：
```javascript
var compression = require('compression')
var express = require('express')
var app = express()
app.use(compression())
```

在高并发的网站生产环境中，  最好的方法是把压缩放在反向代理层面（参考[使用反向代理](https://expressjs.com/en/advanced/best-practice-performance.html#use-a-reverse-proxy)）。 在这种情况下，你不需要使用压缩中间件。如何在Nginx中启用Gzip压缩请参考Nginx文档 [Module ngx_http_gzip_module](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)。
<h3 id="forbidden-synchronous-function">不要使用同步函数</h3>
同步函数和方法阻塞一直到他们返回。一个同步函数返回需要几微秒或几毫秒， 在高并发的站点，这些请求会降低应用的性能。需要避免在生产环境中使用。
虽然 Node和其它许多模块为它们的功能提交了同步和异步版本，永远在产品中使用异步版本。唯一可以使用的是在初始化应用过程。

如果你使用Node.js 4.0 + 或者 io.js 2.1.0+ , 你可以使用--trace-sync-io 命令行标志来输入使用同步API的调用栈告警。 当然，你不想在产品中使用它，当你需要确保你的代码已经准备好了在生产环境中使用。更多信息参考 [Node命令行选项文档](https://nodejs.org/api/cli.html#cli_trace_sync_io)

<h3 id="forbidden-directly-logging">不要记录直接日志</h3>
通常， 记录日志有两个原因：调试和记录应用事件。 在开发过程中使用console.log(), console.error()打印日志消息到终端非常常见。 但是这些函数是同步，当目标是一个终端或者文件的时候，因此，这些不太多我刚看在生产环境中使用，除非你输出放到另一个应用中。

#### 调试日志
如果为了调试记录日志，可以使用一个类似[debug](https://www.npmjs.com/package/debug)的模块来代替console.log()。 这个模块允许你使用DEBUG环境变量来控制哪种类型调试消息发送到console.err(),如果有的话。 为了保证应用完全的异步， 你可能还是需要将console.err()管道到另外一个程序。 但是在那种情况下，你不是真正的在生产环境调试。

#### 应用事件日志
如果为了记录应用事件（如，流量、API调用）等， 可以使用类似[Winston](https://www.npmjs.com/package/winston)或[Bunyan](https://www.npmjs.com/package/bunyan)的日志库来代替使用console.log()。 更多关于这两个库对比的信息可以参才StrongLoop的博客[Comparing Winston and Bunyan Node.js Logging](https://strongloop.com/strongblog/compare-node-js-logging-winston-bunyan/).

<h3 id="properly-handle-exception">正确的处理异常</h3>
Node应用遇到没有被捕获的异常时将会崩溃。如果没有处理异常和合适的动作将会使用Express应用崩溃或离线 。

#### 不要做的事情

#### 使用try-catch

#### 使用promise

## 环境和部署中要做的事情

<h3 id="env-production">NODE_ENV设置了"production"</h3>

<h3 id="auto-restart">确保应用能自动重启</h3>

<h3 id="run-in-cluster">以集群的方式运行应用</h3>

<h3 id="cache-request-result">缓存请求结果</h3>

<h3 id="use-load-balance">使用负载均衡</h3>

<h3 id="use-reverse-proxy">使用反向代理</h3>
