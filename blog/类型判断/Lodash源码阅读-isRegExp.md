> baseIsRegExp 是 isRegExp 浏览器环境下的实现。

## 简介

baseIsRegExp 是 Lodash 内部用于判断一个值是否为正则表达式对象的基础函数。它通过组合使用 isObjectLike 和 baseGetTag 两个工具函数，实现了对正则表达式的精确识别。

## 源码实现

```js
function baseIsRegExp(value) {
  return isObjectLike(value) && baseGetTag(value) == regexpTag;
}
```

## 实现原理解析

### 1. isObjectLike(value)

首先通过 isObjectLike 函数进行初步筛选：

- 确保值是类对象类型（typeof value === 'object' && value !== null）
- 排除基本类型值和 null
- 为后续的标签检查做准备

```js
// 非对象类型直接返回 false
isObjectLike(null); // => false
isObjectLike(undefined); // => false
isObjectLike('abc'); // => false

// 正则表达式对象返回 true
isObjectLike(/abc/); // => true
isObjectLike(new RegExp('abc')); // => true
```

### 2. baseGetTag(value) == regexpTag

通过 baseGetTag 获取对象的内部 [[Class]] 标签，并与正则表达式标签进行比对：

- 使用 baseGetTag 获取对象的原始类型标签
- 与正则表达式标签（'[object RegExp]'）进行比较
- 可以正确识别正则表达式对象，无论是字面量形式还是构造函数创建的形式

```js
// 正则表达式字面量
baseIsRegExp(/abc/); // => true

// RegExp 构造函数
baseIsRegExp(new RegExp('abc')); // => true

// 非正则表达式对象
baseIsRegExp({ source: 'abc' }); // => false
baseIsRegExp('/abc/'); // => false
```




## 总结

baseIsRegExp 函数通过巧妙组合 isObjectLike 和 baseGetTag 两个基础函数，实现了对正则表达式的准确识别。