## 功能概述

isBoolean 函数是 Lodash 中用于类型检测的工具函数，主要用于判断一个值是否为布尔类型。它不仅能识别原始布尔值（true/false），还能正确识别 Boolean 对象实例。

## 源码实现

```js
function isBoolean(value) {
  return (
    value === true ||
    value === false ||
    (isObjectLike(value) && baseGetTag(value) == boolTag)
  );
}
```

## 实现原理解析

### 1. value === true || value === false

原始值判断，函数首先通过严格相等运算符（===）判断值是否为原始布尔值。

### 2. isObjectLike(value) && baseGetTag(value) == boolTag

如果不是原始布尔值，函数会继续检查是否为 Boolean 对象：
包含两个步骤：

1. 通过 `isObjectLike` 确保值是类对象
   - 检查 value 是否为对象
   - 排除 null

2. 使用 `baseGetTag` 获取内部[[Class]]标签
   - 获取对象的原始类型标签
   - 与布尔对象标签（'[object Boolean]'）进行比对

```js
// 原始布尔值
isBoolean(true); // => true
isBoolean(false); // => true

// Boolean 对象
isBoolean(new Boolean(true)); // => true
isBoolean(new Boolean(false)); // => true

// 非布尔值
isBoolean(null); // => false
isBoolean(undefined); // => false
isBoolean(1); // => false
isBoolean("true"); // => false
isBoolean({}); // => false
```

## 总结

isBoolean 函数通过组合使用严格相等比较和对象类型检测，解决了 JavaScript 中 typeof new Boolean(true) 返回 "object" 的歧义问题，实现了对布尔类型的精确识别。
