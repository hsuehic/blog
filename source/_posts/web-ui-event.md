---
title: DOM Event 事件
date: 2018-05-08 17:00:18
tags:
- DOM3
- DOM Event
- MutationEvent
- MutationObserver
- Bubble
- Capture
- Event Phrase
categories:
- web
- dom event
thumbnail: /images/eventflow.svg
barnar:
---


最近一段时间，在面试和被面试过程中都有关于DOM事件的一些交流和沟通，有jQuery对象实例中的事件，也有React组件中的事件，觉得有必要温故一下相关的知识，查看了一些资料，记录下。主要包括以下方面内容。
- DOM事件结构
- 事件类型
- 自定义事件和事件扩展
- 几个特殊的事件

<!-- more -->

UI Events设计主要有两个目标。一是， 设计一个事件系统：允许注册事件监听者，通过树型结构订阅事件。二是，为当前浏览器使用的事件系统定义一个通用的子集，用来增加对已存在脚本和内容的交互。

## DOM 事件结构

### 事件发布和DOM事件流
下面概要的介绍下事件发布机制和事件如何在DOM树中冒泡。应用可以使用dispatchEvent()方法来发布事件对象，事件对象会根据DOM事件流在DOM树中冒泡。
下图描述了事件使用DOM事件流在DOM树中冒泡的过程。
![](/images/eventflow.svg)

事件对象发布到特定的事件目标。在事件可以发布以前，先要确定事件的冒泡路径。
冒泡路径是事件对象传递的当前事件目标的有序列表。这个路径依赖DOM树的结构。列表中最后一个元素是事件源，前面的元素是事件源祖先元素，紧挨着事件源的元素是事件源的父元素。
一旦冒泡路径确定，事件对象开始传递。 事件过程包括3个阶段： 捕获阶段、目标阶段、冒泡阶段。事件对象完成如下面描述的这些阶段。如果鞭一个阶段不被支持或者冒泡被中止了，这个阶段将被忽略。
例如， bubbles 属性被设置成false， 冒泡阶段将被忽略， 或者如果 stopPropagation() 在发布前被调用， 所有的阶段将被忽略。

- 捕获阶段
事件对象从window对象依次通过事件源的祖先元素传播到事件源。
- 目标阶段
事件到达事件对象的事件源。也叫at-target阶段。 如果事件类型中指明了事件不冒泡，事件将在这个阶段结束后停止。
- 冒泡阶段
事件依照逆序在事件源的视先对象中传递，直到window对象结束。

## 事件类型
DOM事件对象模型允许DOM实现多个事件模块。这个模型被设计成允许在未来添加更多的新事件模块。为了交互能力，DOM定义了用户界面事件，包括低级别的依赖设备的事件和DOM改变事件。

### UI事件类型

#### load

接口: 如果是从用户界面元素产生则是UIEvent，否则是Event
同步/异步: 异步
冒泡： 否
元素类型： Window, Document, Element
是否可取消: 否
默认行为： 无

当一个实现这个接口的DOM完成资源加载(例如Document)和任何的依赖资源(如图片、样式文件、脚文件等)客户端必须发布这个事件。依赖资源加载失败不会阻止这个事件的触发。

#### unload

接口: 如果是从用户界面元素产生则是UIEvent，否则是Event
同步/异步: 同步
冒泡： 否
元素类型： Window, Document, Element
是否可取消: 否
默认行为： 无

当一个实现这个接口的DOM从环境中移除时客户端必须发布这个事件。

#### abort

接口: 如果是从用户界面元素产生则是UIEvent，否则是Event
同步/异步: 同步
冒泡： 否
元素类型： Window, Element
是否可取消: 否
默认行为： 无

当用户取消一个正在加载中的资源加载时，客户端必须发布这个事件。

#### error

接口: 如果是从用户界面元素产生则是UIEvent，否则是Event
同步/异步: 异步
冒泡： 否
元素类型： Window, Element
是否可取消: 否
默认行为： 无

当一个资源加载失败或资源不能正确使用，或脚本执行出错，以及不正确的XML格式时，客户端必须发布这个事件。

#### select

接口: 如果是从用户界面元素产生则是UIEvent，否则是Event
同步/异步: 同步
冒泡： 否
元素类型： Element
是否可取消: 否
默认行为： 无

当文本被选中，客户端必须发布这个事件。这个事件在文本已经被选中时发生。



## Focus事件

### 接口定义
FocusEvent 接口提供了几个特定与Focus事件相关的上下文信息。
- .relatedTarget 
依据事件类型，用来表示第二个与Focus事件相关的事件源

### Focus事件顺序

用户切换焦点
1. focusin
在第一事件源接收焦点前触发
2. focus
在第一事件源接收焦点后触发
3. focusout
在第一事件源失去焦点前触发
4. focusin
在第二事件源接收焦点前触发
5. blur
在第一事件源失去焦点后触发
6. focus
在第二事件源接收焦点后触发

### Focus事件类型

####  blur
接口: FocusEvent
同步/异步: 同步
冒泡： 否
元素类型： Window, Element
是否可取消: 否
默认行为： 无

事件源失去焦点客户端必须发布这个事件。这个事件在文本已经被选中时发生。

####  focus
接口: FocusEvent
同步/异步: 同步
冒泡： 否
元素类型： Window, Element
是否可取消: 否
默认行为： 无

事件源接收焦点后客户端必须发布这个事件。这个事件在文本已经被选中时发生。

####  focusin
接口: FocusEvent
同步/异步: 同步
冒泡： 否
元素类型： Window, Element
是否可取消: 否
默认行为： 无

事件源获得焦点前客户端必须发布这个事件。这个事件在文本已经被选中时发生。

####  focusout
接口: FocusEvent
同步/异步: 同步
冒泡： 否
元素类型： Window, Element
是否可取消: 否
默认行为： 无

事件源将要失去焦点客户端必须发布这个事件。这个事件在文本已经被选中时发生。

## 鼠标事件（MouseEvent）

## 滚动事件 (WheelEvent)

## 输入事件 (InputEvent)

## 键盘事件 (KeyboardEvent)

## DOM结构改变事件 (MutationEvent)

Mutation事件设计允许获取文档结构变化的通知，也包括属性、文本和名称 的修改。

### 接口定义
```C++
interface MutationEvent : Event {
  // attrChangeType
  const unsigned short MODIFICATION = 1;
  const unsigned short ADDITION = 2;
  const unsigned short REMOVAL = 3;

  readonly attribute Node? relatedNode;
  readonly attribute DOMString prevValue;
  readonly attribute DOMString newValue;
  readonly attribute DOMString attrName;
  readonly attribute unsigned short attrChange;

  void initMutationEvent();
};
```

### MutationEvent事件类型

####  DOMAttrModified
接口: MutationEvent
同步/异步: 同步
冒泡：是
元素类型： Element
是否可取消: 否
默认行为： 无

节点的属性值被修改，或属性被添加、移除客户端必须发布这个事件。

####  DOMCharacterDataModified
接口: MutationEvent
同步/异步: 同步
冒泡：是
元素类型： Text, Comment, ProcessingInstruction
是否可取消: 否
默认行为： 无

CharactData.data或ProcessingInstruction.data被修改，但节点自身没有被插入或删除时客户端必须发布这个事件。


####  DOMNodeInserted
接口: MutationEvent
同步/异步: 同步
冒泡：是
元素类型：Element, Attr, Text, Comment, DocumentType, ProcessingInstruction
是否可取消: 否
默认行为： 无

当一个节点或属性作为子节点添加到另外一个节点时客户端必须发布这个事件。

####  DOMNodeInsertedIntoDocument
接口: MutationEvent
同步/异步: 同步
冒泡：是
元素类型：Element, Attr, Text, Comment, DocumentType, ProcessingInstruction
是否可取消: 否
默认行为： 无

当一个节点或属性作为子节点添加到一个文档时客户端必须发布这个事件。直接或间接加到文档中都会触发，如所在的父节点被添加到文档中。

####  DOMNodeRemoved
接口: MutationEvent
同步/异步: 同步
冒泡：是
元素类型：Element, Attr, Text, Comment, DocumentType, ProcessingInstruction
是否可取消: 否
默认行为： 无

当一个节点或属性被移除时客户端必须发布这个事件。

####  DOMNodeRemovedFromDocument
接口: MutationEvent
同步/异步: 同步
冒泡：是
元素类型：Element, Attr, Text, Comment, DocumentType, ProcessingInstruction
是否可取消: 否
默认行为： 无

当一个节点或属性从文档中被移除时客户端必须发布这个事件。直接或间接从文档中移除都会触发这个事件。

####  DOMSubtreeModified
接口: MutationEvent
同步/异步: 同步
冒泡：是
元素类型：Window, Document, DocumentFragment, Element, Attr
是否可取消: 否
默认行为： 无

这是一个概括的事件对应任何的文档变化，可以用来替代其它的Mutation事件。


## 几个相关对象介绍
### MutationObserver

#### 构造函数
该构造函数用来实例化一个新Mutation观察者对象。
```javascript
new MutationObserver(
  function callback
);
```
参数

callback 
该回调函数会有指定的DOM节点发生变化时被调用，在调用时，观察者对象会传给该函数两个参数， 第一个参数是包含了若干个MutationRecord对象的数组， 第二个参数则是这个观察者对象本身。

#### 实例方法
- .observe
```C++
void observe(Node target, optional MutationObserverInit options);
```
给当前观察者对象注册需要观察的目标节点，在目标俺不会空（还可以同时观察其后代节点）发生DOM变化时收到通知。

- .disconnect
```C++
void disconnect();
```
让该观察者对象停止观察指定目标的DOM变化，直到再次调用其observe()方法

- .takeRecords
```C++
Array takeRecords();
```
返回值
返回一个包含了MutationRecord的对象数组。

### MutationObserverInit

是一个用来配置观察者对象行为的对象，具有以下属性：

| 属性 | 描述 |
| - | - |
| childList | 如果需要观察目标节点的子节点(新增加了某个子节点或者移除了某个子节点), 则设置为true。 |
| attributes | 如果需要观察目标节点的改改（新增加或删除了某个属性，以及某个属性值发生了变化）, 则设置为true。 |
| characterData | 如果该节点为characterData节点（一种抽象接口， 具体可以为文本节点，注释节点，以及处理指令节点）时，也要观察该节点的文本内容是否发生了变化，则设置为true。 |
| subtree | 除了目标点，如果还需要观察目标节点的所有后代节点（观察目标节点所包含的整棵DOM树上的上述三种节点变化），则设置为true。 |
| attributeOldValue | 在attributes 属性设置为true的前提下，如果需要将发生变化的属性节点之前的属性值记录下来（记录到下面MutationRecord对象的orderValue属性中），则设置为true。 |
| attributeFilter | 一个属性数组(不需要指定命名空间), 只有该数组中包含的属性发生变化时才会被观察到， 其它属性发生变化后会被忽略。 |

### MutationRecord
对象会作为第一个参数传递给观察者对象包含的回调函数，该对象具有以下属性

| 属性 | 类型 | 描述 |
| - | - | - |
| type | String | 如果是属性发生变化,则返回attributes。 如果是一个CharacterData节点发生变化,则返回characterData,如果是目标节点的某个子节点发生了变化,则返回childList。|
| target | Node | 返回此次变化影响到的节点,具体返回那种节点类型是根据type值的不同而不同的. 如果type为attributes,则返回发生变化的属性节点所在的元素节点,如果type值为characterData,则返回发生变化的这个characterData节点.如果type为childList,则返回发生变化的子节点的父节点.|
| addedNodes | NodeList | 返回被添加的节点,或者为null。 |
| removedNodes | NodeList | 返回被删除的节点,或者为null|
| previousSibling | Node | 返回被添加或被删除的节点的前一个兄弟节点,或者为null.|
| nextSibling | Node | 返回被添加或被删除的节点的后一个兄弟节点,或者为null.|
| attributeName | String | 返回变更属性的本地名称,或者为null.|
| attributeNamespace | String | 返回变更属性的命名空间,或者为null.|
| oldValue | String | 根据type值的不同,返回的值也会不同.如果type为 attributes,则返回该属性变化之前的属性值.如果type为characterData,则返回该节点变化之前的文本数据.如果type为childList,则返回null.|



## 参考

- https://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture
- https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver#Instance_methods