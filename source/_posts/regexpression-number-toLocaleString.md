title: 使用正则表达式实现Number.toLocaleString
tags: 
- 正则表达式 
- 正向预查
- Number
- toLocaleString
- Polyfill
categories:
- 前端
- JavaScript
date: 2017/11/06 16:46
thumbnail: /images/to-locale-string.png
---
今天做后台项目需要将数据格式化实现,具体需求就是类似将 12345678 转化成12,345,678显示。 虽然, Number.prototype.toLocaleString方法可以方便的实现此功能。但由于toLocaleString 方法的兼容性问题,还需要实现个Polyfill以兼容低版本浏览器。

<!-- more -->

## Number.prototype.toLocale 功能介绍 

toLocaleString() 方法返回这个数字在特定语言环境下的表示字符串。

新的 locales 和 options 参数让应用程序可以指定要进行格式转换的语言，并且定制函数的行为。在旧的实现中，会忽略 locales 和 options 参数，使用的语言环境和返回的字符串的形式完全取决于实现方式。

![](/images/to-locale-string.png)

## Number.prototype.toLocaleString 兼容性

* 桌面浏览器兼容性
![](/images/number-to-locale-string-desktop.png)

* 移动端浏览器兼容器
![](/images/number-to-locale-string-mobile.png)

## 使用正则表达式正向预查实现简单的Polyfill

```Javascript
if (!Number.prototype.toLocaleString) {
    Number.prototype.toLocaleString = function () {
        let v = this.toString();
        let reg = /\d{1,3}(?=((\d{3})+(\.\d+)?)$)/g;
        v = v.replace(reg, '$&,');
        return v;
    }
}
```