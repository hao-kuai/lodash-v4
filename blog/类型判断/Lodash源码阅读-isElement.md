---
highlight: tomorrow-night-bright
theme: vue-pro
---

## 功能概述

isElement 函数是 Lodash 中用于检测给定值是否为 DOM 元素的工具函数。它通过组合多个条件判断，确保能准确识别各种 DOM 元素，同时排除那些看起来像元素但实际不是的对象。

## 源码实现

```js
function isElement(value) {
  return isObjectLike(value) && value.nodeType === 1 && !isPlainObject(value);
}
```

## 实现原理解析

### 1. 首先确保是类对象

```js
isObjectLike(value)
```

这一步是检查值是否为"类对象"，也就是：
- 不是 `null`
- 类型是 `object`

这个判断很重要，因为只有对象才可能是 DOM 元素，而且可以快速排除一大批明显不是元素的值。

示例：

```js
// 这些都不是 DOM 元素，在第一步就被排除了
isElement(null); // false
isElement(undefined); // false
isElement(42); // false
isElement("div"); // false - 字符串"div"不是DOM元素
isElement(true); // false
isElement(Symbol()); // false

// 这些是对象，可以通过第一步检查
isElement(document.body); // 继续检查
isElement(document.createElement('div')); // 继续检查
```

### 2. 检查 nodeType 属性是否为 1

```js
value.nodeType === 1
```

这一步是 DOM 元素判断的核心，因为：
- 所有的 DOM 元素都有 `nodeType` 属性
- 元素节点的 `nodeType` 值恰好等于 1
- 其他节点类型有不同的值（如文本节点是 3，注释节点是 8）

这个判断非常精确，能区分出真正的元素节点和其他类型的 DOM 节点。

示例：

```js
// 真正的 DOM 元素，nodeType 为 1
var div = document.createElement('div');
console.log(div.nodeType); // 1
isElement(div); // 继续检查

// 文本节点不是元素，nodeType 为 3
var textNode = document.createTextNode('Hello');
console.log(textNode.nodeType); // 3
isElement(textNode); // false

// 注释节点不是元素，nodeType 为 8
var commentNode = document.createComment('注释');
console.log(commentNode.nodeType); // 8
isElement(commentNode); // false

// 文档节点不是元素，nodeType 为 9
console.log(document.nodeType); // 9
isElement(document); // false

// 普通对象没有 nodeType 属性或值不为 1
var fakeElement = { tagName: 'DIV', innerHTML: 'fake' };
console.log(fakeElement.nodeType); // undefined
isElement(fakeElement); // false
```

### 3. 排除普通对象

```js
!isPlainObject(value)
```

这一步是为了防止有人伪造 DOM 元素。有些对象可能会设置 `nodeType = 1` 来冒充元素，但它们实际上只是普通对象，不是真正的 DOM 元素。

`isPlainObject` 用来检测一个对象是否是"纯粹对象"，也就是通过 `{}` 或 `new Object()` 创建的对象。真正的 DOM 元素不是纯粹对象，它们是由浏览器内部创建的特殊对象。

示例：

```js
// 伪造的元素对象，有 nodeType = 1，但是是纯粹对象
var fakeElement = { nodeType: 1, tagName: 'DIV' };
console.log(isObjectLike(fakeElement)); // true
console.log(fakeElement.nodeType === 1); // true
console.log(isPlainObject(fakeElement)); // true
isElement(fakeElement); // false，因为是纯粹对象

// 真正的 DOM 元素不是纯粹对象
var realElement = document.createElement('div');
console.log(isObjectLike(realElement)); // true
console.log(realElement.nodeType === 1); // true
console.log(isPlainObject(realElement)); // false
isElement(realElement); // true，通过所有检查

// 使用 Object.create 创建的对象也可能被伪装成元素
var anotherFake = Object.create(null);
anotherFake.nodeType = 1;
console.log(isPlainObject(anotherFake)); // true (在大多数实现中)
isElement(anotherFake); // false
```

## 总结

isElement 函数通过三个简单但有效的判断条件，准确识别 DOM 元素：

1. 首先确保值是类对象（不是 null 且类型是 object）
2. 然后检查 nodeType 属性是否为 1（元素节点的特征）
3. 最后排除纯粹对象（防止伪造）

这种组合判断既简洁又全面，能够在各种场景下正确识别 DOM 元素，同时排除各种可能的伪装情况。