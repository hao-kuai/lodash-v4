## 功能概述

isSymbol 函数是 Lodash 中用于判断一个值是否为 Symbol 类型的工具函数。它不仅能识别原始的 Symbol 值，还能正确处理 Symbol 对象包装器。

## 源码实现

```js
function isSymbol(value) {
  return (
    typeof value == "symbol" ||
    (isObjectLike(value) && baseGetTag(value) == symbolTag)
  );
}
```

## 实现原理解析

### 1. typeof value == "symbol"

使用 typeof 运算符进行快速判断原始 Symbol 值。

```js
const sym1 = Symbol("foo");
const sym2 = Symbol("bar");

console.log(_.isSymbol(sym1)); // true
console.log(_.isSymbol(sym2)); // true
```

### 2. isObjectLike(value)

确保值是对象类型，排除非对象类型的值（如 null、原始类型），避免 baseGetTag 被意外调用。

```js
console.log(_.isSymbol("foo")); // false（字符串）
console.log(_.isSymbol(123)); // false（数字）
console.log(_.isSymbol(null)); // false（null）
console.log(_.isSymbol(undefined)); // false（undefined）
console.log(_.isSymbol({})); // false（普通对象）
```

### 3. Symbol 对象检测

```js
baseGetTag(value) == symbolTag;
```

- 使用 baseGetTag 获取内部 [[Class]] 标签
- 与 symbolTag（'[object Symbol]'）进行比对

```js
const symObj = Object(Symbol("test"));
isSymbol(symObj); // true（Symbol 对象）

const obj = { [Symbol.toStringTag]: "Symbol" };
isSymbol(obj); // false (baseGetTag 屏蔽 Symbol.toStringTag 并获取原始标签）

const freezeObj = { [Symbol.toStringTag]: "Symbol" };
Object.freeze(freezeObj);
isSymbol(obj); // true (baseGetTag 屏蔽 Symbol.toStringTag 失败）
```

## 总结

isSymbol 函数通过组合使用 typeof 运算符和内部标签检测，实现了对 Symbol 类型的全面识别。其实现既考虑了性能（优先使用 typeof），又保证了准确性（处理 Symbol 对象）。
