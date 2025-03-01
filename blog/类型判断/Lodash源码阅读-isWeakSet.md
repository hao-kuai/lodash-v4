## 功能概述

`isWeakSet` 函数用于检查一个值是否为 WeakSet 对象。WeakSet 是 ES6 引入的一种新的集合类型，它只能存储对象引用，并且是弱引用。这个函数能够准确识别原生 WeakSet 实例，同时也能正确处理继承自 WeakSet 的对象。

## 源码实现

```javascript
function isWeakSet(value) {
  return isObjectLike(value) && baseGetTag(value) == weakSetTag;
}
```

## 实现原理

### 1. isObjectLike 验证

`isObjectLike` 函数会检查传入的值是否为对象类型（排除 null），具体判断规则为：

- 值必须不为 null
- typeof 运算结果必须为 'object'

这一步验证可以快速过滤掉原始类型值（如字符串、数字、布尔值等）和 null，提高函数的执行效率。

### 2. baseGetTag 标签检查

`baseGetTag` 函数用于获取对象的内部 `[[Class]]` 标签。对于 WeakSet 对象，其标签值为 `[object WeakSet]`。这个标签值被存储在 `weakSetTag` 常量中。

通过比较 `baseGetTag` 返回的标签值与 `weakSetTag` 是否相等，可以准确判断对象是否为 WeakSet 实例。

```javascript
// 创建 WeakSet 实例
const ws = new WeakSet();

// 基本使用
isWeakSet(ws); // => true
isWeakSet(new WeakSet()); // => true

// 处理其他类型
isWeakSet(new Set()); // => false
isWeakSet({}); // => false
isWeakSet(null); // => false
isWeakSet(undefined); // => false

// 处理继承情况
class CustomWeakSet extends WeakSet {}
const custom = new CustomWeakSet();
isWeakSet(custom); // => true
```

## 总结

`isWeakSet` 函数通过组合使用 `isObjectLike` 和 `baseGetTag` 两个基础函数，实现了对 WeakSet 类型的精确判断。
