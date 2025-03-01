> baseGetTag 是 getTag 的默认实现。

## 源码实现

```js
function isWeakMap(value) {
  return isObjectLike(value) && getTag(value) == weakMapTag;
}
```

## 实现原理解析

### 1. 对象特性检查

函数首先通过 `isObjectLike` 进行初步筛选：

- 确保传入值为对象类型（typeof value === 'object'）
- 排除 null 值（value !== null）

### 2. 类型标签判断

函数使用 `getTag` 获取并比对类型标签：

```js
getTag(value) == weakMapTag; // weakMapTag 为 '[object WeakMap]'
```

这个判断的特点：

- 通过 getTag 函数获取内部 [[Class]] 属性，确保类型判断的准确性
- 能正确识别原生 WeakMap 实例和继承自 WeakMap 的对象

```js
// 原生 WeakMap 实例
const map1 = new WeakMap();
isWeakMap(map1); // => true

// 继承自 WeakMap 的对象
class CustomWeakMap extends WeakMap {}
const map2 = new CustomWeakMap();
isWeakMap(map2); // => true

// 非 WeakMap 对象
const obj = { key: "value" };
isWeakMap(obj); // => false
```

## 总结

isWeakMap 函数通过组合使用 isObjectLike 和 getTag，实现了对 WeakMap 类型的精确检测。
