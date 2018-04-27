---
title: 优化Web应用性能(三) 渲染过程的性能优化
date: 2018-04-28 15:20:00
tags: Web Performance;
thumbnail:
barnar:
---
## 优化渲染过程
Web应用性能优化前两篇中介绍了Web性能优化分

## 优化渲染过程

### JavaScript 执行优化
- 对于动画效果的实现，避免使用 setTimeout 或 setInterval，请使用 requestAnimationFrame。
- 将长时间运行的 JavaScript 从主线程移到 Web Worker。
- 使用微任务来执行对多个帧的 DOM 更改。
- 使用 Chrome DevTools 的 Timeline 和 JavaScript 分析器来评估 JavaScript 的影响
### 帧
- 将操作处理时间优化到10ms以内
- 使用requestAnimation 来代替setTimeout,setInterval 尽可能在帧开始时招待处理,而不是在帧后段
### 样式计算
- 样式选择器占用了50%左右的时间
- 不使用复杂
- 文档layout flow
### Layout
### Repaint
### 样式嵌套和样式计算复杂度
### 减少重绘区域大小