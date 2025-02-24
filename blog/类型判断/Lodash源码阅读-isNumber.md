## 功能概述

isNumber 函数是 Lodash 中用于判断一个值是否为数字类型的工具函数。它不仅能识别原始数字类型，还能正确处理包装对象（如 new Number(1)）的情况。

## 源码实现

```js
function isNumber(value) {
  return (
    typeof value == "number" ||
    (isObjectLike(value) && baseGetTag(value) == numberTag)
  );
}
```

## 实现原理解析

### 1. typeof value == 'number'

识别原始数字类型

- 通过 typeof 操作符可精准识别原始类型，返回 "number" 表示数字;
- 无法识别数字包装对象（如 new Number(1)）

```js
_.isNumber(3); // true（原始数字）
_.isNumber(Number.MIN_VALUE); // true（有效数字）
_.isNumber(Infinity); // true（但需用 _.isFinite 排除）
```

### 2. isObjectLike(value) && baseGetTag(value) == numberTag

识别对象类型

1. `isObjectLike(value)`：

   - 确保值是类对象类型
   - 排除 null 和基础类型值
   - 为后续的标签检查做准备

2. `baseGetTag(value) == numberTag`：
   - 通过 Object.prototype.toString 获取对象的内部 [[Class]] 标签
   - 与数字类型的标签（'[object Number]'）进行比较
   - 可以正确识别数字包装对象

```js
_.isNumber(new Number(3)); // true（数字对象）
_.isNumber({ valueOf: () => 3 }); // false（标签不符）
```

## 总结

通过 类型过滤 + 标签校验 的策略，精准识别数字类型，适用于需要严格类型判断的场景（如数据清洗、类型验证）。若需排除 Infinity 等特殊值，需结合 `isFinite` 方法
