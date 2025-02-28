> baseIsMap 是 isMap 浏览器环境下的实现。

## 源码实现

```js
function baseIsMap(value) {
  return isObjectLike(value) && getTag(value) == mapTag;
}
```

## 实现原理解析

### 1. isObjectLike(value)

首先通过 isObjectLike 函数进行初步筛选：

- 确保值是类对象类型（typeof value === 'object' && value !== null）
- 排除基本类型值和 null

```js
// 非对象类型直接返回 false
isObjectLike(null); // => false
isObjectLike(undefined); // => false
isObjectLike("abc"); // => false

// Map 对象返回 true
isObjectLike(new Map()); // => true
```

### 2. getTag(value) == mapTag

通过内部标签判断是否为 Map 对象：

1. `getTag(value)`：

   - 使用 Object.prototype.toString 获取对象的内部 [[Class]] 标签
   - 返回形如 '[object Map]' 的字符串

2. `mapTag`：
   - 常量值 '[object Map]'
   - 用于匹配 Map 对象的标准标签

```js
// Map 对象
_.isMap(new Map()); // => true

// 非 Map 对象
_.isMap(new WeakMap()); // => false（标签为 '[object WeakMap]'）
_.isMap(new Set()); // => false（标签为 '[object Set]'）
_.isMap({}); // => false（标签为 '[object Object]'）
```



## 总结

baseIsMap 函数通过巧妙组合 isObjectLike 和 getTag 两个基础函数，实现了对 Map 对象的准确识别。