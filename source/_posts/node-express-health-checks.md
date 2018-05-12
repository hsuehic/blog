---
title: 健康检测和优雅关闭
date: 2018-05-12 15:16:23
tags:
- health checks
- grace shutdown
- terminus
categories:
- node.js
- express
thumbnail: /images/nodejs.png
barnar:
---

## 优雅关闭
你需要部署一个新版本应用时，你需要替换原来的版本。 你使用的进程管理器首先会发一个通知应用它将被关闭的SIGTERM的信号到应用。一旦应用接收到这个信号，它将停止接收新到请求，完成所有的正在执行的请求，清除它使用的资源，包括关闭数据库链接以及释放文件锁。
<!-- more -->

## 健康检测

负载均衡使用健康检查来判断一个应用实例是否健康，能否接收和处理请求。 例如， [Kubernetes具有两项健康检测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)：
- liveness. 判断 是否需要重启一个容器。
- readiness. 判断 一个容器是否准备好了接收流量。 当产品不可用时， 它将被从负载均衡中移除。

## 第三方解决方案：terminus

[Terminus](https://github.com/godaddy/terminus)是一个开源的健康检测和优雅关闭的产品， 消除重复编写模版代码的需求。只需要提供优雅关闭清除逻辑和健康检测逻辑实现，terminus将会完成剩余的事情。

使用如下方式安装:
```bash
npm i @godaddy/terminus --save
```

下面示例展示了如果使用terminus. 更多信息， 请参考 [https://github.com/godaddy/terminus](https://github.com/godaddy/terminus)。

```bash
const http = require('http');
const express = require('express');
const terminus = require('@godaddy/terminus');

const app = express();

app.get('/', (req, res) => {
  res.send('ok');
});

const server = http.createServer(app);

function onSignal() {
  console.log('server is starting cleanup');
  // start cleanup of resource, like databases or file descriptors
}

async function onHealthCheck() {
  // checks if the system is healthy, like the db connection is live
  // resolves, if health, rejects if not
}

terminus(server, {
  signal: 'SIGINT',
   healthChecks: {
    '/healthcheck': onHealthCheck,
  },
  onSignal
});

server.listen(3000);
```