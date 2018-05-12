---
title: Express应用的进程管理器
date: 2018-05-12 12:18:25
tags:
- node.js
- express
- process manager
- forever
- pm2
- StrongLoop Process Manager
- SystemD
categories:
- node.js
- express
thumbnail: /images/nodejs.png
barnar:
---

当Express应用运行在生产环境时， 使用一个进程管理器来达到以下目标是非常有帮助的：
- 当应用崩溃时，重启应用
- 获取应用的运行性能和资源点用情况
- 动态修改应用设置以改进运用的性能
- 管理集群

进程管理器类似于应用服务器：是一个方便应用的部署，提供可用性，允许在运行时管理应用的应用容器。
<!-- more -->

下面是Express应用和其它node.js应用最流行的进程管理器：

- [Forever](#forever)
- [PM2](#pm2)
- [StrongLoop Process Manager](#strongloop-process-manager)
- [SystemD](#systemd)

使用上述4个中的任一个都非常有帮助，其中StrongLoop Process Manager 是其中唯一提供完整的运行时和部署解决方案的。它管理Node.js应用的整个生命周期，为发布成到生产环境前后的每一步在统一的界面中提供工具。

下面是对于这个工具的简短的介绍。查看更多的详细信息， 参阅 [http://strong-pm.io/compare/](http://strong-pm.io/compare/)。

<h2 id="forever">Forever</h2>

Forever 是一个保证指定脚本持续运行的一个简单的命令行工具。Forever的简洁的界面使它成为一个部署运行小Node.js应用和脚本的好的选项。

更多信息，请参考 [https://github.com/foreverjs/forever](https://github.com/foreverjs/forever)。

### 安装
```bash
$ [sudo] npm install forever -g
```

### 基本使用
使用forever的启动命令行和脚本路径启动一个脚本：
```bash
$ forever start path/to/script.js
```

这个命令行会使用守护进程模式运行脚本(在后台进程)。

如果要在当前终端运行脚本，可以省略start:
```bash
$ forever script.js
```

更好的做法是使用Forever工具记录日志输出，如下所示在命令行中添加-l, -o, -e选项：
```bash
$ forever start -l forever.log -o out.log -e err.log path/to/script.js
```

查看所有使用Forever启动的脚本：
```bash
$ forever list
```

停止使用Forever启动的脚本可以使用Forever的停止命令行 指定进程的索引（在forever list命令行列表中可以查看）
```bash
$ forever stop 1
```
或者也可以指定脚本的路径：
```bash
$ forever stop path/to/script.js
```
停止所有使用Forever启动的脚本：
```bash
$ forever stopall
```

Forever提供了非常多的设置，也提代的可编程的API。

<h2 id="pm2">PM2</h2>

PM2是一个Node.js应用生产环境的进程管理器，它内置了负载均衡。PM2保持应用一直运行，可以在不停止服务的下重新加载，同时也方便一些通用的系统任务管理。 PM2可以管理应用的日志、监控和集群。

更多信息， 请参考 [https://github.com/Unitech/pm2](https://github.com/Unitech/pm2)。

### 安装
```bash
$ [sudo] npm install pm2 -g
```

### 基本使用

使用pm2命令行启动应用时， 必须指定应用的路径。在停止，重启，删除应用时可以只需要指定应用名或者应用的Id。
```bash
$ pm2 start app.js
[PM2] restartProcessId process id 0
┌──────────┬────┬──────┬───────┬────────┬─────────┬────────┬─────────────┬──────────┐
│ App name │ id │ mode │ pid   │ status │ restart │ uptime │ memory      │ watching │
├──────────┼────┼──────┼───────┼────────┼─────────┼────────┼─────────────┼──────────┤
│ my-app   │ 0  │ fork │ 64029 │ online │ 1       │ 0s     │ 17.816 MB   │ disabled │
└──────────┴────┴──────┴───────┴────────┴─────────┴────────┴─────────────┴──────────┘
 Use the `pm2 show <id|name>` command to get more details about an app.
```

全用pm2命令行启动应用，应用立即在后台运行。可以使用多种多样的pm2命令行来管理在后台运行的应用。

pm2启动应用行，在pm2的应用列表中注册并得到了一个ID。 你可以使用这些IDs在不同的环境和目录下管理这些应用。

如果有超过一个应用具有相同的name,在同时运行， pm2命令行执行会影响全部具有相同name的应用，因此，建议使用IDs替代name来管理不同的应用。

列出所有正在运行的进程：
```bash
$ pm2 list
```

停止一个应用：
```bash
$ pm2 top 0
```

重启一个应用：
```bash
$ pm2 restart 0
```

显示一个应用的详细信息：
```bash
$ pm2 show 0
```

从pm2注册列表中移除一个应用:
```bash
$ pm2 delete 0
```
<h2 id="strongloop-process-manager">StrongLoop Process Manager</h2>

StrongLoop Process Manage(StrongLoop PM) 是一个Node.js应用生产环境进程管理器。 StrongLoop PM 内置了负载均衡， 监控和多服务器部署，也提提供了一个图形界面的控制台。可以使用StrongLoop PM来完成以下任务：

- 构建、打包和部署Node.js应用到本地或远程环境
- 查看CPU性能内存快照以优化性能和发现内存泄漏
- 保持进程和集群持续运行
- 查看应用性能矩阵
- 内置nignx方便的管理多个应用服务器
- 可以将多个StrongLoop PM 以分布式微服务的方式使用Arc统一管理。

可以使用叫slc的强大的命令行接口来使用StrongLoop PM， 也可以使用一个叫Arc的图形化工具来使用StrongLoop PM. Arc已经开源，StrongLoop提供专业的支持。

更多信息，参考 [http://strong-pm.io/](http://strong-pm.io/)

完整的文档： 

- [Operating Node apps (StrongLoop documentation)](http://docs.strongloop.com/display/SLC)
- [Using StrongLoop Process Manager](http://docs.strongloop.com/display/SLC/Using+Process+Manager)

### 安装
```bash
$ [sudo] npm install -g strongloop
```

### 基本用法
```bash
$ cd my-app
$ slc start
```

查看进程管理器的状态和所有的部署的应用:
```bash
$ slc ctl
Service ID: 1
Service Name: my-app
Environment variables:
  No environment variables defined
Instances:
    Version  Agent version  Cluster size
     4.1.13      1.5.14           4
Processes:
        ID      PID   WID  Listening Ports  Tracking objects?  CPU profiling?
    1.1.57692  57692   0
    1.1.57693  57693   1     0.0.0.0:3001
    1.1.57694  57694   2     0.0.0.0:3001
    1.1.57695  57695   3     0.0.0.0:3001
    1.1.57696  57696   4     0.0.0.0:3001
```

列出管理的所有的应用(服务):
```bash
$ slc ctl ls
Id          Name         Scale
 1          my-app       1
```

停止一个应用
```bash
$ slc ctl stop my-app
```

重启一个应用：
```bash
$ slc ctl restart my-app
```

也可以“软重启”, 这样允许正在运行的进程有一个优雅的时间来关闭已经有链接后，再重启应用。
```bash
$ slc ctl soft-restart my-app
```
从管理器中移除一个应用:
```bash
$ slc ctl remove my-app
```

<h2 id="systemd">SystemD</h2>

### 介绍

SystemD是现代Linux版本中默认的进程管理器。基于SystemD运行一个Node.js应用非常简单。下面的内容引自 [Ralph Slooten (@axllent)的一篇博客](https://www.axllent.org/docs/view/nodejs-service-with-systemd/)。

### 创建服务
创建文件/etc/systemd/system/express.service:
```
[Unit]
Description=Express
# Set dependencies to other services (optional)
#After=mongodb.service

[Service]
# Run Grunt before starting the server (optional)
#ExecStartPre=/usr/bin/grunt

# Start the js-file starting the express server
ExecStart=/usr/bin/node server.js
WorkingDirectory=/usr/local/express
Restart=always
RestartSec=10
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=Express
# Change to a non-root user (optional, but recommended)
#User=<alternate user>
#Group=<alternate group>
# Set environment options
Environment=NODE_ENV=production PORT=8080

[Install]
WantedBy=multi-user.target
```

### 使服务可用
```bash
$ systemctl enable exress.service
```

### 启动服务
```bash
$ systemctl start express.service
```

### 查看服务状态
```bash
$ systemctl status express.service
```
