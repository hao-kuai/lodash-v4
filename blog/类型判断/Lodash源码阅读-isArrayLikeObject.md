---
highlight: tomorrow-night-bright
theme: vue-pro
---

## 功能概述

isArrayLikeObject 函数是 Lodash 中用于检测给定值是否为"类数组对象"的工具函数。它通过组合 isObjectLike 和 isArrayLike 两个基础函数，确保检测的值不仅具有类数组的特征（有 length 属性且为非负整数），还必须是一个对象类型。

## 源码实现

```js
function isArrayLikeObject(value) {
  return isObjectLike(value) && isArrayLike(value);
}
```

## 实现原理解析

### 1. 两个条件的组合判断

这个函数的实现非常简洁，但包含了两个重要的判断条件：

#### 1.1 必须是类对象（isObjectLike）

```js
isObjectLike(value);
```

首先确保值是一个对象类型，且不为 null。这个判断可以过滤掉一些基本类型值。例如：

```js
// 以下都不是对象类型，返回 false
isArrayLikeObject(null); // false
isArrayLikeObject(undefined); // false
isArrayLikeObject(42); // false
isArrayLikeObject("hello"); // false
isArrayLikeObject(true); // false

// 以下是对象类型，继续进行后续判断
isArrayLikeObject([1, 2, 3]); // true
isArrayLikeObject(document.querySelectorAll("div")); // true
isArrayLikeObject({ 0: "a", 1: "b", length: 2 }); // true
```

#### 1.2 必须是类数组（isArrayLike）

```js
isArrayLike(value);
```

然后判断这个对象是否具有类数组的特征，主要检查：

- 是否有 length 属性
- length 属性是否为非负整数
- length 属性值是否小于等于 Number.MAX_SAFE_INTEGER

示例：

```js
// 虽然是对象，但不是类数组，返回 false
isArrayLikeObject({}); // false
isArrayLikeObject({ length: -1 }); // false
isArrayLikeObject({ length: "abc" }); // false

// 是对象且是类数组，返回 true
isArrayLikeObject(document.getElementsByTagName("div")); // true
isArrayLikeObject(new Array(3)); // true
isArrayLikeObject({ 0: "a", 1: "b", length: 2 }); // true

// 特殊情况：函数虽然有 length 属性，但不被视为类数组对象
isArrayLikeObject(function () {}); // false
```

### 2. 常见的类数组对象

在实际开发中，最常见的类数组对象包括：

1. DOM 集合对象：

```js
// NodeList
const nodeList = document.querySelectorAll("div");
isArrayLikeObject(nodeList); // true

// HTMLCollection
const htmlCollection = document.getElementsByClassName("item");
isArrayLikeObject(htmlCollection); // true
```

2. 自定义类数组对象：

```js
const arrayLikeObj = {
  0: "first",
  1: "second",
  length: 2,
};
isArrayLikeObject(arrayLikeObj); // true
```

3. Array 的子类实例：

```js
class MyArray extends Array {}
const myArr = new MyArray();
isArrayLikeObject(myArr); // true
```

## 总结

isArrayLikeObject 函数通过巧妙地组合 isObjectLike 和 isArrayLike 两个基础判断，实现了对类数组对象的精确识别。
