> 此处 isNaN 是_.isNaN，不是全局的 isNaN。

## 功能概述

isNaN 函数是 Lodash 中用于检测一个值是否为 NaN（Not a Number）的工具函数。它通过巧妙的类型检查和值比较，解决了 JavaScript 原生 isNaN 函数可能误判的问题。

## 源码实现

```js
function isNaN(value) {
  // An `NaN` primitive is the only value that is not equal to itself.
  // Perform the `toStringTag` check first to avoid errors with some
  // ActiveX objects in IE.
  return isNumber(value) && value != +value;
}
```

## 实现原理解析

### 1. isNumber(value)

确保值是数字类型（包括原始值和包装对象）。

```js
isNumber(NaN); // true
isNumber("abc"); //  false
```

### 2. value != +value

- 利用 NaN 是唯一一个不等于自身的值的特性
- 使用一元加号运算符确保操作数被转换为原始数字类型

```js
NaN != +NaN; // true，其他数值均不满足此条件
```

### 3. 示例

```js
console.log(_.isNaN(NaN)); // true
console.log(_.isNaN(new Number(NaN))); // true（包装对象内部值为 NaN）
console.log(_.isNaN(undefined)); // false（严格检查类型）
console.log(isNaN(undefined)); // true（全局方法误判）
```

## 总结

通过这种双重验证，Lodash 的 isNaN 实现了与 Number.isNaN 一致的行为，同时提供了更严格的类型校验。
