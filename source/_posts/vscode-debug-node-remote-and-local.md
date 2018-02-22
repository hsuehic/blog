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

## VSCode 的Debugging介绍 

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
1. 在左侧导航Tab选择调试,打开调试面板
2. 在调试Toolbar中点击设置, 打开launch.json使其处于编辑状态
3. 根据工程目录配置
![](/images/vscode_local.png)

## 使用VSCode 远程调试Node.js 应用


### Kill Scripts Signal events
- 在已经运行的Node应用进程中开启调试
1. 查找pid
```bash
ps ls | grep node
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

