## 功能概述

`isFinite` 函数用于检查给定的值是否为有限数值。它结合了类型检查和原生 `isFinite` 方法，确保返回值的准确性。

## 源码实现

```javascript
function isFinite(value) {
  return typeof value == "number" && nativeIsFinite(value);
}
```

## 实现原理解析

### 1. typeof value == 'number'

进行类型检查，确保输入值的类型为 'number'。仅包含原始值，不包括数字对象。

```javascript
_.isFinite(3); // true
_.isFinite(new Number(3)); // false; typeof new Number(3) 是 'object'
```

### 2. nativeIsFinite(value);

```javascript
nativeIsFinite(value);
// 浏览器环境等效于
window.isFinite(value);
```

在类型检查通过后，使用原生的 `isFinite` 方法（这里用 `nativeIsFinite` 表示）进行进一步判断：

- 检查数值是否为有限值
- 排除 `Infinity`、`-Infinity` 和 `NaN` 等特殊数值

```javascript
_.isFinite(3); // true
_.isFinite(Infinity); // false
_.isFinite(NaN); // false
```

### 3. vs Number.isFinite

功能一样，但是兼容性更好，兼容的浏览器版本更多。

### 4. vs window.isFinite

不会进行隐式类型转换。

```javascript
console.log(window.isFinite("123")); // true（字符串转为数值 123）
console.log(Number.isFinite("123")); // false（非数值类型）
console.log(_.isFinite("123")); // false（Lodash 严格检查类型）[1,4,5](@ref)
```

## 总结

`isFinite` 函数通过组合类型检查和原生方法调用，实现了对有限数值的精确判断。
