## 功能概述

isObject 函数是 Lodash 中一个核心的类型判断函数，主要用于判断给定值是否为对象类型。不仅可以识别普通对象，还可以正确判断函数、数组等特殊对象类型。

## 源码实现

```js
function isObject(value) {
  // 获取参数的类型；
  var type = typeof value;
  // 其中 null 不是对象；函数是一等公民，是特殊对象
  return value != null && (type == "object" || type == "function");
}
```

## 实现原理解析

### 1. 类型获取

函数首先通过 `typeof` 操作符获取值的类型：

```js
var type = typeof value;
```

这一步骤的特点：

- 使用 `typeof` 操作符可以快速获取值的基本类型
- `typeof` 返回的可能值包括：'undefined'、'object'、'boolean'、'number'、'string'、'function'、'symbol'
- 对于 `null`，`typeof` 会返回 'object'，这是 JavaScript 的一个历史遗留问题

### 2. 空值排除

```js
value != null;
```

这个条件检查有两个作用：

- 排除 `null` 值，因为 `typeof null` 会返回 'object'
- 使用 `!=` 而不是 `!==`，这样可以同时排除 `null` 和 `undefined`

### 3. 类型判断

```js
type == "object" || type == "function";
```

这个判断逻辑：

- 检查类型是否为 'object' 或 'function'
- 匹配普通对象（普通对象、数组、正则等）
- 匹配函数（包括普通函数、箭头函数、生成器函数、异步函数等）

## 总结

isObject 函数通过简洁而高效的实现，准确地识别了 JavaScript 中的对象类型。它的实现特点包括：

1. 使用 typeof 进行基础类型检测
2. 通过空值检查避免 null 的误判
3. 同时支持普通对象和函数类型
