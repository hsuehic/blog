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

<h2 id="tsl">使用TSL</h2>

<h2 id="helmet">使用Helmet</h2>

<h2 id="cookies">安全的使用cookies</h2>

<h2 id="dependencies">确保依赖资源是安全的</h2>

<h2 id="know-vulnerabilities">防止其它已知的安全漏洞</h2>

<h2 id="more">更多可以考虑的方面</h2>