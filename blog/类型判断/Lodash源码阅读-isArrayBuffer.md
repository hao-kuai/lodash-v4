---
highlight: tomorrow-night-bright
theme: vue-pro
---

> baseIsArrayBuffer 是 isArrayBuffer 函数在浏览器环境的基实现。

## 功能概述

isArrayBuffer 函数是 Lodash 中用于检测给定值是否为 ArrayBuffer 对象的工具函数。它通过组合 isObjectLike 和 baseGetTag 两个基础函数，实现了对 ArrayBuffer 类型的精确判断。

## 源码实现

```js
function baseIsArrayBuffer(value) {
  return isObjectLike(value) && baseGetTag(value) == arrayBufferTag;
}
```

## 实现原理解析

### 1. 类型检测的双重保障

源码中采用了两层检测机制：

```js
isObjectLike(value) && baseGetTag(value) == arrayBufferTag;
```

这种实现方式具有两重保障：

#### 1.1 对象特征检测（isObjectLike）

首先通过 `isObjectLike(value)` 确保值具有对象的基本特征：

```js
// 这些都不是 ArrayBuffer，会在第一步就返回 false
const notArrayBuffers = [null, undefined, 42, "string", true, Symbol("test")];

notArrayBuffers.forEach((value) => {
  console.log(isArrayBuffer(value)); // 全部返回 false
});

// 创建一个真实的 ArrayBuffer
const buffer = new ArrayBuffer(8);
console.log(isObjectLike(buffer)); // true，通过第一层检测
```

#### 1.2 内部标签检测（baseGetTag）

通过 `baseGetTag(value) == arrayBufferTag` 进行更精确的类型判断：

```js
// 虽然这些都是对象，但它们的内部标签都不是 [object ArrayBuffer]
const objectLikes = [{}, [], new Date(), new RegExp("test"), new Int8Array(4)];

objectLikes.forEach((value) => {
  // 虽然都通过了 isObjectLike 检测
  console.log(isObjectLike(value)); // true
  // 但 baseGetTag 的结果与 arrayBufferTag 不匹配
  console.log(isArrayBuffer(value)); // false
});

// 而真实的 ArrayBuffer 两项检测都能通过
const buffer = new ArrayBuffer(16);
console.log(isArrayBuffer(buffer)); // true
```

#### 1.3 特殊情况处理

```js
// 1. 继承自 ArrayBuffer 的对象
class CustomBuffer extends ArrayBuffer {}
const customBuffer = new CustomBuffer(8);
console.log(isArrayBuffer(customBuffer)); // true，正确识别继承类型

// 2. 类 ArrayBuffer 对象
const fakeBuffer = {
  byteLength: 8,
  slice: ArrayBuffer.prototype.slice,
};
console.log(isArrayBuffer(fakeBuffer)); // false，识破伪装

// 3. 被转换的 ArrayBuffer
const buffer = new ArrayBuffer(8);
const bufferObj = Object(buffer);
console.log(isArrayBuffer(bufferObj)); // true，正确处理包装对象
```

通过这些示例，我们可以看到 isArrayBuffer 函数在各种场景下都能准确判断一个值是否为真实的 ArrayBuffer 对象。
