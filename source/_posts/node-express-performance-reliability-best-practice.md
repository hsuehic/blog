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

<h3 id="forbidden-synchronous-function">不要使用同步函数</h3>

<h3 id="forbidden-directly-logging">不要直接日志</h3>

#### 调试日志

#### 应用事件日志

<h3 id="properly-handle-exception">正确的处理异常</h3>

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
