---
title: 前端全栈之路--使用Node.js与React仿微信的视频IM应用
date: 2018-08-03 19:17:26
tags:
- WebRTC
- 全栈
categories:
- JavaScript
- Performance
thumbnail: /images/react.png
---

直入主题，使用一周的时间使用前端相关的技术做一个仿微信的IM应用。
<!--more -->
具体的效果如下：
线上地址：[ https://chat.gismall.com/](https://chat.gismall.com/)
前端仓库地址：[https://github.com/hsuehic/react-wechat](https://github.com/hsuehic/react-wechat)
后端仓库地址：[https://github.com/hsuehic/react-wechat-backend](https://github.com/hsuehic/react-wechat-backend)
![wechat-2-0-30.gif](http://ata2-img.cn-hangzhou.img-pub.aliyun-inc.com/d1858e417a491b43b2047cd7393c0594.gif)

涉及的技术主要包括以下：
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

想与大的交流的点主包括以下内容：
- WebRTC的一些基本概念
- 使用Node.js与WebSocket构建WebRTC信令服务器
- 使用NoSQL数据存储设计与关系型数据库的差异
- Node.js应用的发布和热更新
- JSONWebToken在前后端分离应用及WebSocket中鉴权

欢迎大家拍砖！

## 1. WebRTC的一些基本概念
### 1.1  WebRTC相关的协议
#### 1.1.1 ICE
ICE, 全名 Interactive Connectivity Establishment。是一个允许浏览器之间建议交互的点对点连接的框架。点A与点B之间不能简单直接连接的原因有很多。 首先，需要穿过可能阻止连接的防火墙，其次在大多数情况下设备需要一个唯一的公有地址，另外就是路由器不允许点点对的数据直接传递。ICE使用STUN或者(和)TURN服务器解决这些问题。
#### 1.1.2 STUN
STUN, 全名Session Traversal Utilities for NAT (STUN) 。是一个用来发现公共地址和路由对点对点直连限制的服务。
客户端(浏览器)向STUN服务器发送一个请求，然后STUN服务器会返回客户端的公网地址和客户端所在路由NAT是否可用。
![webrtc-stun.png](/images/webrtc-stun.png)
#### 1.1.3 NAT
NAT,全名Network Address Translation (NAT) 。是一个网络地址转换协议。就是给连接到路由的设备一个私有地址，并且通路由且的公网IP使用特定端口转发使用设置在公网上可以被发现和访问。有些路由会限连接到私有网络。
#### 1.1.4 TURN
TURN全名Traversal Using Relays around NAT (TURN)。有些使用NAT的路由会使用一种被称为“对称NAT”的限制。在这种情况下需要有一个中继服务器分别与终端建立连接并转发数据。
![webrtc-turn.png](/images/webrtc-turn.png)
#### 1.1.5 SDP
SDP全名Session Description Protocol (SDP) 。对多媒体连接的一种标准描述，如分辨率、格式、解码器、加密等，使数据传输时连接两端都了解对方。实质上，SDP是传输数据的元数据。
### 1.2 信令和视频呼叫
WebRTC允许实时点对点的视频传输。
#### 1.2.1 信令交互时序
![WebRTC - Signaling Diagram.png](/images/WebRTC-Signaling-Diagram.png)
#### 1.2.2 视频请求交互时序
![WebRTC - ICE Candidate Exchange.png](/images/WebRTC-ICECandidate-Exchange.png)
### 1.3 WebRTC主要的接口
#### 1.3.1 RTCPeerConnection
表示本地设备到远端的WebRTC连接。提供了连接到远端Peer的方法，维护、监视、关闭连接等。
#### 1.3.2 RTCConfiguration
RTCPeerConnection的配置信息。
#### 1.3.3 RTCDataChannel
关连到RTCPeerConnection的通道，用来传输任意类型的数据。
## 2. 使用Node.js与WebSocket构建WebRTC信令服务器
### 2.1 Koa Websock中间件
需要使用koa支持websocket路由以及上下文使用。相关的middleware代码如下。
```js
/**
 * @File   : koa-websocket.js
 * @Author : Richard Hsueh <xiaowei.hsueh@gmail.com> (https://www.gistop.com/)
 * @Link   : http://www.gistop.com/
 * @Date   : 2018-7-1 16:31:26
 */
const url = require('url');
const https = require('https');
const compose = require('koa-compose');
const co = require('co');
const ws = require('ws');

const WebSocketServer = ws.Server;
const debug = require('debug')('koa:websockets');

function KoaWebSocketServer(app) {
  this.app = app;
  this.middleware = [];
}

KoaWebSocketServer.prototype.listen = function listen(options) {
  this.server = new WebSocketServer(options);
  this.server.on('connection', this.onConnection.bind(this));
};

KoaWebSocketServer.prototype.onConnection = function onConnection(socket, req) {
  debug('Connection received');
  socket.on('error', err => {
    debug('Error occurred:', err);
  });
  const fn = co.wrap(compose(this.middleware));

  const context = this.app.createContext(req);
  context.websocket = socket;
  context.path = url.parse(req.url).pathname;

  fn(context).catch(err => {
    debug(err);
  });
};

KoaWebSocketServer.prototype.use = function use(fn) {
  this.middleware.push(fn);
  return this;
};

module.exports = function middleware(app, wsOptions, httpsOptions) {
  const oldListen = app.listen;
  app.listen = function listen(...args) {
    debug('Attaching server...');
    if (typeof httpsOptions === 'object') {
      const httpsServer = https.createServer(httpsOptions, app.callback());
      app.server = httpsServer.listen(...args);
    } else {
      app.server = oldListen.apply(app, args);
    }
    const options = { server: app.server };
    if (wsOptions) {
      Object.keys(wsOptions).forEach(key => {
        if (Object.prototype.hasOwnProperty.call(wsOptions, key)) {
          options[key] = wsOptions[key];
        }
      });
    }
    app.ws.listen(options);
    return app.server;
  };
  app.ws = new KoaWebSocketServer(app);
  return app;
};
```

### 2.2 聊天功能及信令转发实现
#### 2.2.1 信令类型定义
```js
const RTC_MESSAGE_TYPE = {
  CANDIDATE: 'new-ice-candidate',
  HANG_UP: 'hang-up',
  VIDEO_OFFER: 'video-offer',
  VIDEO_ANSWER: 'video-answer',
  INVITE_OFFER: 'invite-offer',
  INVITE_ACCEPT: 'invite-accept',
  INVITE_REFUSE: 'invite-refuse',
  INVITE_CANCEL: 'invite-cancel'
};
```
#### 2.2.2 WebSocket路由及消息处理
```js

websocket.get('/wechat/:token', async(ctx, next) => {
  const { token } = ctx.params;
  const { secret } = configs;
  const user = await verify(token, secret, { ignoreExpiration: true });
  ctx.mongo = mongo;
  // 已经登录通过难
  if (user) {
    const { phone } = user;
    sockets.set(phone, ctx.websocket);
    await sendInitialData(ctx, user);
    ctx.websocket.on('message', message => {
      try {
        const msg = JSON.parse(message);
        const { type } = msg;
        const { payload } = msg;
        const { to } = payload;
        switch (type) {
          case 'candidate':
            sendCandidate(ctx, user, msg);
            break;
          case 'wechat/saveMessage':
            sendMessage(ctx, user, msg);
            break;
          case RTC_MESSAGE_TYPE.CANDIDATE:
          case RTC_MESSAGE_TYPE.HANG_UP:
          case RTC_MESSAGE_TYPE.VIDEO_OFFER:
          case RTC_MESSAGE_TYPE.VIDEO_ANSWER:
          case RTC_MESSAGE_TYPE.INVITE_ACCEPT:
          case RTC_MESSAGE_TYPE.INVITE_CANCEL:
          case RTC_MESSAGE_TYPE.INVITE_OFFER:
          case RTC_MESSAGE_TYPE.INVITE_REFUSE:
            sendMessageToClient(to, message);
            break;
          default:
            break;
        }
      } catch (e) {
        console.error(e);
      }
    });
    ctx.websocket.on('close', () => {
      sockets.delete(phone);
    });
  } else {
    // 未登录的情况直接关闭socket链接
    ctx.websocket.close();
  }
  await next();
});
```
#### 2.2.3 离线消息处理
```js
/**
 * 保存离线消息
 * @param {object} ctx 上下文
 * @param {object} user 当前会话用户
 * @param {object} msg 消息对象
 */
const saveOfflineMessage = async(ctx, user, msg) => {
  const now = new Date();
  const { payload } = msg;
  const { to, content, from, timestamp } = payload;
  const { phone } = user;
  const conversationModel = new ConversationModel(ctx);
  const selector = {
    phone: to
  };
  const prefix = `conversation.${phone}`;
  const document = {
    $set: {
      [`${prefix}.timestamp`]: now.getTime()
    },
    $inc: {
      [`${prefix}.newCount`]: 1
    },
    $addToSet: {
      [`${prefix}.items`]: {
        content,
        to,
        from,
        timestamp
      }
    }
  };
  const options = {
    upsert: true
  };
  const p = conversationModel.update(selector, document, options);
  p.then(() => {
    console.log('保存离线消息成功！');
  }).catch(error => {
    console.log('保存离线消息失败！');
    console.error(error);
  });
};
```

#### 2.2.4 初始化数据
主要包括联系人信息和离线会话
```js
/**
 * 发送初始化数据
 * @param {Application} ctx 上下文对象
 */
const sendInitialData = async(ctx, user) => {
  const userModel = new UserModel(ctx, user);
  const conversations = await userModel.getConversationList();
  if (conversations) {
    ctx.websocket.send(JSON.stringify({
      type: 'wechat/saveConversation',
      payload: {
        conversations
      }
    }));
  }
  const contacts = await userModel.getContactList();
  ctx.websocket.send(JSON.stringify({
    type: 'wechat/save',
    payload: {
      contacts
    }
  }));
};
```

## 3. 使用NoSQL数据存储设计与关系型数据库的差异
> IM应用中需要存储类似于用户、联系人、对话、对话内容、联系人设置等信息

在以前使用关系型型数据库时代，我们可能会根据范式思想使用不少映射表来存类似于朋友、对话、备注、设置等信息，在二维的信息中表示关系可能会很复杂，为检索性能需要小心的设置索引，查询时也需要做很多的连接查询。

在NoSQL时代，MongoDB等非关系型数据库，支持以Document的形式存储数据，可以很方便的以JSON节点的形式表示对象之的关系，也支持复杂、功能丰富的子节点检索功能，在数据结构的设计方面比RDB有很大的优势。
比事以下是一个聊天信息的数据结构。
```json
/* 3 */
{
    "_id" : ObjectId("5b483da404739f15a38387ed"),
    "phone" : "18958067915",
    "conversation" : {
        "18958067917" : {
            "items" : [ 
                "你好！", 
                {
                    "content" : "fdfdfd",
                    "to" : "18958067915",
                    "from" : "18958067917",
                    "timestamp" : 1533300341213.0
                }, 
                {
                    "content" : "fjdlfjd;",
                    "to" : "18958067915",
                    "from" : "18958067917",
                    "timestamp" : 1533300342076.0
                }, 
                {
                    "content" : "fdjfldjf",
                    "to" : "18958067915",
                    "from" : "18958067917",
                    "timestamp" : 1533300342933.0
                }, 
                {
                    "content" : "fjdkj;",
                    "to" : "18958067915",
                    "from" : "18958067917",
                    "timestamp" : 1533300343925.0
                }, 
                {
                    "content" : null,
                    "to" : "18958067915",
                    "from" : "18958067917",
                    "timestamp" : 1533300344110.0
                }
            ],
            "timestamp" : 1533300344110.0,
            "newCount" : 6.0
        },
        "18958067916" : {
            "items" : [ 
                "你好！"
            ],
            "timestamp" : 1531460398135.0,
            "newCount" : 1.0
        }
    }
}
```

后面有时间再针对这个IM应用的数据结构设计展开介绍。


## 4. Node.js应用的发布和热更新
先贴上代码(deploy.sh)。
```shell
#!/bin/sh

# Author : Richard
# Copyright (c) Tutorialspoint.com
# Script follows here:
# Reference: https://www.tutorialspoint.com/unix/unix-file-management.htm

echo upload files to the server

echo copy scripts
host=www.gismall.com
root=/root/apps/wechat
distination=root@$host:$root
scp -r ./bin $distination
scp -r ./controllers $distination
scp -r ./middlewares $distination
scp -r ./models $distination
scp -r ./routes $distination
scp -r ./services $distination
scp -r ./utils $distination
scp -r ./views $distination
scp app.js $distination
scp configs.js $distination
scp package-lock.json $distination
scp package.json $distination
scp README.md $distination
scp yarn.lock $distination
ssh root@$host << remotessh
  cd $root
  npm install
  pm2 restart wechat
  exit
remotessh
```
以上代码只是我发布我服务器端应用的代码。
更新发布时只要执行以下代码就行了。
```bash
./deploy.sh
```
更新只是合适pm2直拉重启，很low的方法与Node.js的热更新无关。
下面再谈谈我了解的Node.js应用热更新。

## 5. JSONWebToken在前后端分离应用及WebSocket中鉴权

  > JSON Web Token (JWT) is a compact, URL-safe means of representing
   claims to be transferred between two parties.  The claims in a JWT
   are encoded as a JSON object that is used as the payload of a JSON
   Web Signature (JWS) structure or as the plaintext of a JSON Web
   Encryption (JWE) structure, enabling the claims to be digitally
   signed or integrity protected with a Message Authentication Code
   (MAC) and/or encrypted.

以上是JSONWebToken的定义

### 5.1  Json Web Token是干什么
       简称JWT，在HTTP通信过程中，进行身份认证。
       我们知道HTTP通信是无状态的，因此客户端的请求到了服务端处理完之后是无法返回给原来的客户端。因此需要对访问的客户端进行识别，常用的做法是通过session机制：客户端在服务端登陆成功之后，服务端会生成一个sessionID，返回给客户端，客户端将sessionID保存到cookie中，再次发起请求的时候，携带cookie中的sessionID到服务端，服务端会缓存该session（会话），当客户端请求到来的时候，服务端就知道是哪个用户的请求，并将处理的结果返回给客户端，完成通信。
      通过上面的分析，可以知道session存在以下问题：
      1. session保存在服务端，当客户访问量增加时，服务端就需要存储大量的session会话，对服务器有很大的考验；
      2. 当服务端为集群时，用户登陆其中一台服务器，会将session保存到该服务器的内存中，但是当用户的访问到其他服务器时，会无法访问，通常采用缓存一致性技术来保证可以共享，或者采用第三方缓存来保存session，不方便。

### 5.2 Json Web Token是怎么做的
      1. 客户端通过用户名和密码登录服务器；
      2. 服务端对客户端身份进行验证；
      3. 服务端对该用户生成Token，返回给客户端；
      4. 客户端将Token保存到本地浏览器，一般保存到cookie中；
      5. 客户端发起请求，需要携带该Token；
      6. 服务端收到请求后，首先验证Token，之后返回数据。
      - 服务端不需要保存Token，只需要对Token中携带的信息进行验证即可；
      - 无论客户端访问后台的那台服务器，只要可以通过用户信息的验证即可。

### 5.3 WebSocket 连时通过 JSON Web Token 鉴权

这一块我没有找到什么好的方法实现。
我目前的实现方法是在websocket连接的路由中加上token, 服务器端在接到连接请求时，判断token是否有效，如有效则发送相应的数据并保存连接，如token无效则关闭连接。