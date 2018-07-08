---
title: 手把手教你用React和NodeJS开发一个仿微信的IM应用（一）
date: 2018-07-08 19:27:43
tags:
- Web
- NodeJS
- React
- Antd-mobile
- WebRTC
- WebSocket
- PWA
categories:
- Web
- Web Application
thumbnail: /images/pwa.png
barnar:
---
计划在团队内部做一个使用webrtc做实时音视频聊天实现的分享。既然是分享，就得有App演示和提供现场体验吧。要给别人看，界面总得做一个吧？ 没有UX, 因此，想到了直接Fake一个Webchat用户界面： 一是个人非常喜欢微信界面，设计得很简单，易用，组件抽象得很好，实现也相对简单。二是刚好可以练习下h5开发。到厂内后，只参与了一个toC的互动和几个toB的小移动项目的开发，这次刚好可学习下。 刚开始API想部分调用web.wechat的API减少工作量，抓包看了下还是挺复杂的，因此，还是决定自己写反正也需要写信令服务器，刚好可以一起。
<!-- more -->

<p>标题有些夸张， 原预计一周左右能仿一个具体微信基本功能的IM应用出来。但实际开发过程，花了三个多周末了，才完成80%左右。</p>

<p>应用的效果如下。</p>

<p>应用开发过程中，欢迎一起玩耍，欢迎 Fork, PR, Start。</p>

前端仓库：[hsuehic/react-wechat](https://github.com/hsuehic/react-wechat) <br />

后端他库：[hsuehic/react-wechat-backend](https://github.com/hsuehic/react-wechat-backend)<br />

demo地址：<br />

[https://chat.gismall.com](https://chat.gismall.com)<br />

手机浏览器扫一扫：<br />
![](/images/wechat.png)

大致效果如果下：<br />

![](/images/wechat-1.gif)
<br />
![](/images/wechat-2.gif)
<br />

涉及到的主要技术栈包括：

- React
- Dva
- Antd-Mobile
- NodeJS
- Koa
- WebSocket
- WebRTC
- JsonWebToken
- Redis
- MongoDB
- Nginx
- PM2
- PWA

后面大致会按照以下方面来介绍整个过程。

- 1. 缘起
- 2. 计划
- 3. 选型
  - 3.1 react、dva、antd-mobile
  - 3.2 koa
  - 3.3 redis、 mongodb
  - 3.4 websocket、WebRTC

- 4. 开发
  - 4.1 UI组件开发
  - 4.2 路由及业务逻辑
  - 4.3 http服务
  - 4.4 WebSocket 服务  
  - 4.5 WebRTC p2p
  - 4.6 安全
    - 4.6.1 信息存储安全
    - 4.6.2 信息传输安全
    - 4.6.3 防攻击

  - 4.7 性能
    - 前端性能优化
    - 后端性能优化、服务器配置优化

- 5. 发布

  - 5.1 静态资源 Nginx
  - 5.2 后台服务 PM2, Nginx Proxy
  - 5.3 稳定性 Cluster && Load Balance



