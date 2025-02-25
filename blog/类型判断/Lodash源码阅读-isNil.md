# Lodash 源码阅读-isNil

## 功能概述

isNil 函数是 Lodash 中用于检查值是否为 null 或 undefined 的工具函数。它与 isNull 和 isUndefined 函数共同构成了 Lodash 处理空值检测的完整体系。

## 实现原理解析

### 1. isUndefined 函数

```js
function isUndefined(value) {
  return value === undefined;
}
```

这个函数使用严格相等运算符（===）来判断一个值是否为 undefined：

- 使用严格相等确保类型和值都完全相同
- 直接与 undefined 进行比较，不会有类型转换

```js
console.log(_.isUndefined(void 0)); // true（void 0 是标准的 undefined 表示方式）
console.log(_.isUndefined(null)); // false（null 和 undefined 不同）
console.log(_.isUndefined(NaN)); // false（NaN 是数值类型）
```

常用于检测变量是否未声明或显式赋值为 undefined 。

### 2. isNull 函数

```js
function isNull(value) {
  return value === null;
}
```

同样采用严格相等运算符（===）来判断值是否为 null：

- 确保与 null 的精确匹配
- 避免与 undefined 或其他假值混淆

```js
console.log(_.isNull(null)); // true
console.log(_.isNull(undefined)); // false（null 和 undefined 不同）
console.log(_.isNull("")); // false（空字符串是字符串类型）
```

### 3. isNil 函数

```js
function isNil(value) {
  return value == null;
}
```

这是三个函数中设计最巧妙的一个：

- 使用宽松相等运算符（==）来同时检测 null 和 undefined
- 利用了 JavaScript 中 null == undefined 为 true 的特性
- 一行代码优雅地解决了两种空值的检测需求

```js
const obj = { age: null, name: undefined };
console.log(_.isNil(obj.age)); // true（age 是 null）
console.log(_.isNil(obj.name)); // true（name 是 undefined）
console.log(_.isNil("")); // false（空字符串是字符串类型）
```

常用于表单验证或对象属性检查，快速判断值是否为空或缺失。

## 总结

- isUndefined 和 isNull 分别处理具体的空值类型
- isNil 作为一个更高层的抽象，统一处理所有空值情况
- 三个函数的实现都保持了简洁性和高性能
