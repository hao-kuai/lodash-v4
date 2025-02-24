## 功能概述

isString 函数是 Lodash 中用于判断一个值是否为字符串类型的工具函数。它不仅能识别原始字符串类型，还能正确处理字符串对象（String 对象实例）。

## 源码实现

```js
function isString(value) {
  return (
    typeof value == "string" ||
    (!isArray(value) && isObjectLike(value) && baseGetTag(value) == stringTag)
  );
}
```

## 实现原理解析

### 1. typeof value == "string"

- 快速识别原始字符串类型
- 能准确识别字面量和 String() 函数创建的字符串

```js
_.isString("abc"); // true（原始字符串）
_.isString(String("abc")); // true（原始字符串）
_.isString(123); // false（数字类型）
```

### 2. !isArray(value)

数组虽属于对象类型（typeof [] 为 "object"），但其内部结构与字符串对象不同，需通过 Array.isArray 或 !isArray 过滤。

```js
"use strict";
const arr = [];
Object.defineProperty(arr, Symbol.toStringTag, {
  value: "String",
  writable: false,
  configurable: false,
});
Object.freeze(arr);
// 严格模式下，会报错，然后会导致 baseGetTag 返回 '[object String]',造成误判
arr[Symbol.toStringTag] = undefined;
```

### 3. isObjectLike(value)

确保值是类似对象的值（非 null 且 typeof 为 "object"）,为后续的 baseGetTag 调用做准备

### 4. baseGetTag(value) == stringTag(value)

- 通过内部标签判断是否为字符串对象
- 使用 baseGetTag 获取准确的类型标签
- 与字符串标签（'[object String]'）进行比对

```js
_.isString(new String("abc")); // true（标签匹配 "[object String]"）
_.isString({}); // false（标签不符）
```

## 总结

仅当值是原始字符串或字符串对象时返回 true，避免误判其他对象类型（如数组、普通对象）。
