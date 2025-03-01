> baseIsSet 是 isSet 浏览器环境下的实现。
> baseGetTag 是 getTag 的默认实现。

## 功能概述

baseIsSet 函数是 Lodash 中用于判断一个值是否为 Set 类型的内部工具函数。它通过组合使用 isObjectLike 和 getTag 两个基础函数，实现了对 Set 类型的精确识别，能够正确处理普通 Set 对象和继承自 Set 的对象。

## 源码实现

```js
function baseIsSet(value) {
  return isObjectLike(value) && getTag(value) == setTag;
}
```

## 实现原理解析

### 1. 对象特征检查

函数首先通过 `isObjectLike(value)` 进行初步筛选：

- 确保传入值为对象类型（typeof value === 'object'）
- 排除 null 值（value !== null）
- 这一步骤可以快速过滤掉原始类型值和 null，提高函数执行效率

### 2. 类型标签判断

通过 `getTag(value) == setTag` 进行精确类型判断：

- getTag 函数返回对象的内部 [[Class]] 标签
- setTag 常量值为 '[object Set]'
- 使用 == 而不是 === 是为了兼容不同 JavaScript 引擎的实现

```js
// 普通 Set 对象
const set = new Set([1, 2, 3]);
baseIsSet(set); // => true

// 继承自 Set 的对象
class CustomSet extends Set {}
const customSet = new CustomSet();
baseIsSet(customSet); // => true

// 非 Set 对象
baseIsSet({}); // => false
baseIsSet([]); // => false
baseIsSet(null); // => false
```

## 总结

baseIsSet 函数通过组合使用 isObjectLike 和 getTag 两个基础函数，实现了对 Set 类型的精确识别。
