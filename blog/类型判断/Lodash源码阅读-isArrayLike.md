## 功能概述

isArrayLike 函数用于判断一个值是否为类数组对象的工具函数。它通过检查值的 length 属性特征来识别类数组对象，是数组相关操作中的重要基础函数。类数组对象包括数组、arguments 对象、DOM NodeList 等具有 length 属性且非函数的对象。

## 源码实现

```js
function isArrayLike(value) {
  return value != null && isLength(value.length) && !isFunction(value);
}
```

## 实现原理解析

### 1. value != null

类数组对象必须存在且非空值，排除 null 和 undefined 类型。

```js
isArrayLike(null); // false（排除 null）
isArrayLike(undefined); // false（排除 undefined）
```

### 2. isLength(value.length)

类数组对象的 length 属性需为有效整数（≥0 且 ≤ Number.MAX_SAFE_INTEGER），用于表示元素数量或索引范围。

```js
isArrayLike("abc"); // true（字符串 length=3，符合整数条件）
isArrayLike([1, 2, 3]); // true（数组 length=3，符合条件）
isArrayLike({ length: 3.5 }); // false（非整数）
isArrayLike({ length: Number.MAX_SAFE_INTEGER + 1 }); // false（超出安全范围）
```

### 3. !isFunction(value)

排除函数类型（如 Function、GeneratorFunction 等），因函数虽有 length 属性但用途不同,函数 length 属性表示参数个数，而非类数组的元素数量。

```js
isArrayLike(function () {}); // false（函数被排除）
isArrayLike(async function () {}); // false（异步函数同样被排除）
```

## 总结

仅当值存在、length 合法且非函数时，才视为类数组，避免逻辑错误。
