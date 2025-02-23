## 功能概述

isObjectLike 函数是 Lodash 中一个基础的类型判断函数，用于检测给定值是否为类对象类型。

## 源码实现

```js
function isObjectLike(value) {
  return value != null && typeof value == "object";
}
```

## 实现原理解析

### 1. 空值排除

函数首先通过 `value != null` 进行空值检查：

```js
value != null;
```

这个条件检查的特点：

- 使用宽松相等运算符 `!=`，可以同时排除 `null` 和 `undefined`
- 避免了 `typeof null === 'object'` 的误判
- 确保了后续类型检查的安全性

### 2. 类型判断

```js
typeof value == "object";
```

这个判断有以下几个关键点：

- 使用 `typeof` 操作符获取值的类型
- 严格匹配 'object' 字符串
- 不同于 `isObject`，不包含函数类型

### 3. 判断逻辑分析

通过组合这两个条件，isObjectLike 可以准确识别：

```js
// 返回 true 的情况
isObjectLike({}); // true
isObjectLike([1, 2, 3]); // true
isObjectLike(new Date()); // true
isObjectLike(new Error()); // true

// 返回 false 的情况
isObjectLike(null); // false
isObjectLike(undefined); // false
isObjectLike(function () {}); // false
isObjectLike("hello"); // false
isObjectLike(42); // false
```

## 总结

isObjectLike 函数通过简洁的实现完成了类对象类型的检测，其特点包括：

1. 使用空值检查避免了常见的 null 误判
2. 通过 typeof 实现基础类型过滤
3. 与 isObject 形成互补
4. 为其他类型检测函数（如 isArrayLike、isPlainObject 等）提供基础支持
