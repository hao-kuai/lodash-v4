## 功能概述

主要用于判断一个值是否为有效的数组长度或类数组对象的长度。

## 源码实现

```js
function isLength(value) {
  return (
    typeof value == "number" &&
    value > -1 &&
    value % 1 == 0 &&
    value <= MAX_SAFE_INTEGER
  );
}
```

## 实现原理解析

### 1. 类型检查

函数首先通过 `typeof value == 'number'` 确保输入值为数字类型。

```js
isLength("3"); // false（字符串类型）
isLength(true); // false（布尔类型）
```

### 2. 有效性验证

#### 2.1 value > -1

数组长度不能为负数或非数字值（如 NaN）

```js
isLength(-1); // false（负数）
isLength(NaN); // false（非数字值）
```

#### 2.2 value % 1 == 0

数组长度必须是整数，不能是小数或浮点数。

```js
isLength(3.5); // false（小数类型）
isLength(4); // true（整数）
```

#### 2.3 vvalue <= MAX_SAFE_INTEGER

限制数值的上限，避免安全整数范围外的值。Number.MAX_SAFE_INTEGER（9007199254740991）是 JavaScript 中可精确表示的最大整数。

```js
isLength(Number.MAX_SAFE_INTEGER + 1); // false（超出安全范围）
```

## 总结

该函数通过 _类型过滤_ + _数值校验_ 的组合策略，精准识别有效的类数组长度。
