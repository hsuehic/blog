---
title: 使用VSCode 本地及远程调试Node.js应用
date: 2018-02-22 15:45:08
tags:
- vscode
- debug
- node
- remote debug
categories:
- 前端
- node.js
- IDE
thumbnail: /images/debugging_hero.png
barnar: /images/debugging_hero.png
---

记录下使用VSCode 调试Node.js应用相关的内容, 包括Node debugger相关的Source maps, remote debug等。
<!-- more -->

## VSCode 的Debugging介绍 

VSCode 的主要特征之一就是其强大的调试支持功能。

## Node.js Inspect 介绍 
有许多的工具和类库可以用来帮助调试Node.js应用。下面列出了一些。
如果不使用工具可以在命令行中添加--inspect标识,然后通过输入的URL连接调试。
![](/images/node_inspect_flag.png)
对于没有使用--inspect启动的Node.js应用,可以传递SIGUSR1信令来激活调试,和打印出连接URL。
![](/images/kill_signal.png)
![](/images/print_url.png)

### Inspect Tools & Clients
[https://nodejs.org/en/docs/inspector/](https://nodejs.org/en/docs/inspector/)

#### node-inspect

## 使用VSCode 调试Node.js 应用

[https://code.visualstudio.com/docs/nodejs/nodejs-debugging](https://code.visualstudio.com/docs/nodejs/nodejs-debugging)

VSCode 编辑器内置支持Node.js运行时调试支持,可以调试JavaScript、TypeScript和很多其它可以编译成JavaScript的编程语言。使用VS Code 提供的默认配置和代码片段可以非常简单的设置好Node.js的调试工程。Node.js调试文档提供了更多的关于node.js调试场景的介绍。 你可以找到Source Map, Stepping over external code, 和远程调试等更多调试相关的内容。

由于VSCode Node.js调试器与Node.js运行时通过wire protocol通信,支持的运行时集合由所有支持wire protocol的运行时确定。

当前存在两种wire protocol.
* legacy: 原生的V8 Debugger Protocol
* inspector: 新的V8 Inspector Protocol 可以使用--inspect开启. 解决原生V8 debugger protocol 大多数的限制和可扩展性问题。

当前运行时对协议的支持情况如下。

| Runtime | 'Legacy' Protocol | 'Inspector' Protocol |
| ------- | ----------------- | -------------------- |
| io.js   | all               | no                   |
| node.js | < 8.x             | >= 6.3(windows: >=6.9 |
| Electron| < 7.4             | >= 7.4                |
| Chakra  | all               | not yet               |

虽然,看起来VS Code Node.js 调试器可以自动选择最合适的协议, 我们还是提供了一个指定特定协议的启动配置属性。
* <span style="color:#f00">auto</span>: 尝试自动检测运行时使用的协议。 当版本为8.0或者更新时,使用'inspector' 协议。 当配置中request 类型为‘attach’时, 尝试使用'inspector'协议,如果可用,则使用。由于更早的版本存在几个问题,仅仅当版本>= 6.9时,才使用'inspector'协议。

* <span style="color:#f00">legacy</span>: 强制node 调试器使用 'legacy'协议。 当node 版本 < v8.0 和Electron 版本< 7.4的时候支持。

* <span style="color:#f00">legacy</span>: 强制node 调试器使用 'inspector'协议。 当node 版本 >= 6.3时支持。 目前 Electron 不支持。

1. 在左侧导航Tab选择调试,打开调试面板
2. 在调试Toolbar中点击设置, 打开launch.json使其处于编辑状态
3. 根据工程目录配置
![](/images/vscode_local.png)

## 使用VSCode 远程调试Node.js 应用

### Kill Scripts Signal events
- 在已经运行的Node应用进程中开启调试
1. 查找pid

```bash
ps | grep node
```
2. 发送信号 启用调试

```bash
kill -s SIGUSR1 nodepid
```

SIGUSR1 参考
[https://nodejs.org/api/process.html#process_signal_events](https://nodejs.org/api/process.html#process_signal_events)

### 在VSCode调试

1. 打开调试面板
2. 新增调试配置
```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "program": "${file}"
        },
        // 新增加远程调试节点
        {
            "type": "node",
            "request": "launch",
            "name": "Remote",
            "protocol": "auto",
            "address": "localhost", // 修改成对应的远程服务器地址
            "port": 9229
        }
    ]
}
```
![](/images/vscode_launch_config.png)

3. 启动调试
- 在调试toolbar中选择Remote启动方式
![](/images/vscode_remote.png)
- 点击启动

## Command Line Options
下表列出了在调时不同标识的作用。

| 标识 | 作用 |
| --- | --- |
| --inspect | <ul><li>启用Inspector 客户端 </li> <li>监听默认的地址和端口（127.0.0.1:9229） </li></ul> |
| --inspect=[host:port] | <ul><li>启用Inspector 客户端</li><li>绑定指定的地址（127.0.0.1）</li><li>监听指定端口（9229）</li></ul> |
| --inspect-brk | <ul><li>启用Inspector 客户端</li><li>监听默认的地址和端口（127.0.0.1:9229）</li><li>在代码开始执行处进入断点</li></ul> |
| --inspect-brk=[host:port] | <ul><li>启用Inspector 客户端</li><li>绑定指定的地址（127.0.0.1）</li><li>监听指定端口（9229）</li><li>在代码开始执行处进入断点</li></ul> |
| node inspect script.js | <ul><li>使用--inspect标识在子进程执行用户代码;在主进程中运行CLI调试</li></ul> |

## 其它

### 使用NIM(Node.js 调试管理工具)
- 可以自动监听本地的9229端口并自动启动Node.js调试窗口
- 参考[https://chrome.google.com/webstore/detail/nodejs-v8-inspector-manag/gnhhdgbaldcilmgcpfddgdbkhjohddkj](https://chrome.google.com/webstore/detail/nodejs-v8-inspector-manag/gnhhdgbaldcilmgcpfddgdbkhjohddkj)

### 关闭远程调试的inspect
