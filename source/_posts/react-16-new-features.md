---
title: React v16 的新特性 (一) React 16.0.0
date: 2018-05-28 23:17:22
tags:
- React 16
- ErrorBoundary
- React.unstable_Profiler
- Map
- Set
- requestAnimationFrame
categories:
- JavaScript
- React
thumbnail: /images/react.png
barnar:
---

这一年都每天忙着业务， 也没有时间看社区的新内容。前几天（2018/05/23）React发布了16.4.0，才发现自己好久没有关注相关的内容了，正好找时间看下16.0.0版本以来的新特性。
16.0.0 版本主要更新包括以下几方面：

- 新的JavaScript运行环境依赖
- 新特性
  - 组件render方法可以返回数组和字符串
  - 优化了错误处理， 增加了错误边界组件 （Error Boundaries)
  - 增加了ReactDOM.createPortal方法支持将子Dom树直接渲染到其它的DOM
  - 流模式的SSR
  - React DOM 允许非标准的属性
- 不兼容的改变
- 移除的废弃内容
<!-- more -->



## 新的JavaScript运行环境依赖

React 16依赖集合类型Map和Set,还有requestAnimationFrame, 如果需要兼容老的不原生支持这些特性的浏览器和设备，需要引入相应的polyfill。

使用core-js polyfilled环境支持老浏览器的示例如下。

```JavaScript
import 'core-js/es6/map';
import 'core-js/es6/set';

import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```

使用raf 包来的shim requestAnimationFrame示例
```JavaScript
import 'raf/polyfill';
```

下面顺便温故下相关的内容。

### Map

Map对象保存键值对，任何值，对象或原始值都可以作为一个键或一个值。键的比较基于"SameValueZero"算法: NaN 与NaN相同，虽然 （NaN !== NaN),剩余所有其它值都是按按===运算结果判断是否相等。

#### 与Obejct对象比较
Object和Map类似的是，都允许按键存取一个值 、删除键、检测一个键是否绑定了值。因此，过去一直把对象当成Map使用，当然也没有其它内建的替代方法。不过， Map和Object有一些重要的区别，在以下情况，Map会是更好的选择。
- 一个对象的键只能是字符串或者Symbols, 但一个Map的键可以是任意值，包括函数、对象、基本类型。
- 可以通过size属性直接获取一个Map的键值对个数，而Object的键值对个数只能手动计算。
- Map 是可迭代的， 而Object的迭代需要先获取它的键数组然后再进行迭代。
- Obeject都有自己的原型， 所以原型链上的键名可能和对象上的键名产生冲突。虽然ES5开始可以用map = Object.create(null)来创建一个没有原型的对象，但是这种方法不常见。
- Map在涉及频繁增删键值对的场景下会有些性能优化。


#### 原型属性
- Map.prototype.constructor 默认为Map函数
- Map.prototype.size

#### 原型方法
- Map.prototype.clear
- Map.prototype.delete(key)
- Map.prototype.entries()
- Map.prototype.forEach(callbackFn[, thisArg])
  callbackFn(key, value)
- Map.prototype.get(key)
- Map.prototype.has(key)
- Map.prototype.keys()
- Map.prototype.set(key, value)
- Map.prototype.values()
- Map.prototype[@@iterator]

#### 兼容性

- 桌面浏览器
![](/images/map-browser-compatible-desktop.png)

- 移动浏览器
![](/images/map-browser-compatible-mobile.png)

### Set

Set对象允许存储任意类型的唯一值，无论是原始值还是引用值。
NaN和Undefined可以被存储在Set中， NaN之间被视为相同的值。

#### 构造函数

```JavaScript
new Set([Iterable])
```

#### 原型属性
- Set.prototype.constructor
默认为 Set
- Set.prototype.size
默认为 0

#### 原型方法

- Set.prototype.add(value)
- Set.prototype.clear()
- Set.prototype.delete(value)
- Set.prototype.entries()
- Set.prototype.forEach(callbackFn[, thisArg])
- Set.prototype.has(value)
- Set.prototype.keys()
- Set.prototype.values()
- Set.prototype[@@iterator]

#### 浏览器兼容性

![](/images/set-browser-compatible-desktop.png)
![](/images/set-browser-compatible-mobile.png)

### window.requestAnimationFrame

requestAnimationFrame方法请求浏览器在下一次重绘之前调用指定的函数来更新动画。 该方法使用一个回调函数作为参数，这个回调函数会在游览器重绘前调用。
回调的次数通常是每秒60次， 但大多数浏览器通常匹配W3C所建议的刷新频率。 在大多数浏览器里当运行在后台标签页或者隐藏的<iframe>里时， requestAnimationFrame()会暂停调用以提升性能和电池寿命。

回调函数会被传入一个参数。 DOMHighResTimeStamp, 指示当前被requestAnimationFrame()排序的回调函数被触发的时间。即使每个回调函数的工作量计算都花了时间， 单个帧中的多个回调也都将被传入相同的时间戳。该时间戳是一个十进制数，单位毫秒， 最小精度为1ms.

#### 浏览器兼容性
- 桌面浏览器
![](/images/request-animation-frame-browser-compatible-desktop.png)

- 移动浏览器
![](/images/request-animation-frame-browser-compatible-mobile.png)

## 新特性

### 组件render方法可以返回数组和字符串
在以前一个组件render方法返回的根元素只能有一个。如需要返回多个同级元素，需要在外层包一个节点，这样就增加了DOM的层级数，支持数组后，就不存在这个问题了。
### 优化了错误处理， 增加了错误边界组件 （Error Boundaries)
### 增加了ReactDOM.createPortal方法支持将子Dom树直接渲染到其它的DOM
### 流模式的SSR
### React DOM 允许非标准的属性


## 不兼容的改变

## 移除的废弃内容



