---
title: React Context 使用
date: 2018-06-21 23:17:22
tags:
- React
- Context
- Provider
categories:
- JavaScript
- React
thumbnail: /images/react.png
barnar:
---

Context 提代了一个向整个组件树传递数据的方法，而不需要通过属性向下一层一层的手动传递。

在一个典型的React应用中， 数据从父组件到子组件通过属性传递，这种方法对于某些特定的在应用中许多组件都需要的属性（比如，locale引用，UI主题等）来说可能会显得比较笨重。 Context提供了一个组件间共享值的方法，而不需要明确的向整个树的每一层都传递一个属性。
<!-- more -->

## 1. 什么时候使用

## 2. API

### 2.1 React.createContext
### 2.2 Privider
### 2.3 Consumer

## 3. 示例

### 3.1 Dynamic Context
### 3.2 Updating Context from a Nested Component
### 3.3 Accessing Context in Lifecycle Method
### 3.4 Consuming Context with a HOC
### 3.5 Forwarding Refs to Context Consumers

## 4. 需要注意的地方

