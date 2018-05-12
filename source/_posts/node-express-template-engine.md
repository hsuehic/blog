---
title: 开发Express模版引擎
date: 2018-05-12 12:00:05
tags:
- express template
- engine
categories:
- node.js
- express
thumbnail: /images/nodejs.png
barnar:
---

使用app.engine(ext, callback) 方法创建根据模版扩展名自定义的模版引擎。第二个参数 callback回调函数是模版引擎函数，它接收如下参数：
- 文件路径(filePath)
- 设置选项(options)
- 回调函数(callback)

下面的代码是一个实现非常简单可以渲染.ntl文件的模版引擎示例。
<!-- more -->
```javascript
  var fs = require('fs') // this engine requires the fs module
  app.engine('ntl', function (filePath, options, callback) { // define the template engine
    fs.readFile(filePath, function (err, content) {
      if (err) return callback(err)
      // this is an extremely simple template engine
      var rendered = content.toString().replace('#title#', '<title>' + options.title + '</title>')
      .replace('#message#', '<h1>' + options.message + '</h1>')
      return callback(null, rendered)
    })
  })
  app.set('views', './views') // specify the views directory
  app.set('view engine', 'ntl') // register the template engine
```
这样应用就可以渲染.ntl的文件了。在views目录下创建一个命名为index.ntl的文件。文件内容如下。
```
#title#
#message#
```
然后在应用中添加如下路由。
```javascript
  app.get('/', function (req, res) {
    res.render('index', { title: 'Hey', message: 'Hello there'})
  })
```
当你请求主页时，index.ntl 会被渲染成HTML。