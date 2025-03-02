---
highlight: tomorrow-night-bright
theme: vue-pro
---

## 功能概述

isArguments 函数是 Lodash 中用于检测给定值是否为 arguments 对象的工具函数。它通过组合标签检测和特性检测两种策略，确保在不同 JavaScript 环境下都能准确识别 arguments 对象。

## 源码实现

```js
var isArguments = baseIsArguments(
  (function () {
    return arguments;
  })()
)
  ? baseIsArguments
  : function (value) {
      return (
        isObjectLike(value) &&
        hasOwnProperty.call(value, "callee") &&
        !propertyIsEnumerable.call(value, "callee")
      );
    };

function baseIsArguments(value) {
  return isObjectLike(value) && baseGetTag(value) == argsTag;
}
```

## 实现原理解析

### 1. 两种判断方式的巧妙结合

来看看这段代码：

```js
var isArguments = baseIsArguments(
  (function () {
    return arguments;
  })()
)
  ? baseIsArguments
  : function (value) {
      // 降级实现...
    };
```

这里的思路其实很巧妙：

- 首先测试基于标签的检测方法（baseIsArguments）是否在当前环境有效
- 如果有效，则使用更高效的标签检测
- 如果无效，则降级使用基于特性的检测

其中，`baseIsArguments(function() { return arguments; }())` 这段代码的实现原理是：

1. `function() { return arguments; }` 定义了一个返回 arguments 对象的匿名函数
2. 通过立即执行函数 `()` 调用这个匿名函数，获得一个真实的 arguments 对象
3. 将这个真实的 arguments 对象传入 baseIsArguments 函数进行测试
4. 如果 baseIsArguments 能够正确识别这个真实的 arguments 对象（返回 true），说明当前环境支持通过标签检测识别 arguments 对象
5. 这种运行时的环境检测确保了在不同的 JavaScript 引擎中都能选择最合适的实现方式

示例：

```js
// 创建一个真实的 arguments 对象进行测试
function testFunc() {
  return arguments;
}
var realArgs = testFunc(1, 2, 3);

// 在大多数现代浏览器中，这里会使用 baseIsArguments
console.log(isArguments(realArgs)); // true
```

### 2. 标签检测方式（baseIsArguments）

```js
function baseIsArguments(value) {
  return isObjectLike(value) && baseGetTag(value) == argsTag;
}
```

这个检测方式分两步走：

#### 2.1 先看看是不是像对象

```js
isObjectLike(value);
```

这一步就是确保这个值看起来像个对象（不是 null，typeof 得到 'object'）。来看几个例子：

```js
// 这些都不是 arguments，肯定返回 false
isArguments(null); // false
isArguments(undefined); // false
isArguments(42); // false
isArguments("string"); // false
```

#### 2.2 再看看标签对不对

```js
baseGetTag(value) == argsTag;
```

这步是通过 Object.prototype.toString 看看这个对象的内部标签是不是 '[object Arguments]'：

```js
// 在支持标准 arguments 对象的环境中
function foo() {
  console.log(Object.prototype.toString.call(arguments)); // '[object Arguments]'
}
foo(1, 2, 3);
```

### 3. 特征检测方式（备选方案）

```js
function(value) {
  return isObjectLike(value) && hasOwnProperty.call(value, 'callee') &&
    !propertyIsEnumerable.call(value, 'callee');
}
```

这个方案就是看看这个值是不是具备 arguments 对象的特征，主要检查三点：

#### 3.1 还是先看看像不像对象

```js
isObjectLike(value);
```

跟前面一样，确保是个对象类型的值。

#### 3.2 看看有没有 callee 属性

```js
hasOwnProperty.call(value, "callee");
```

arguments 对象有个特别的属性叫 callee，指向当前正在执行的函数：

```js
// arguments 对象特有的 callee 属性
function example() {
  console.log(arguments.callee === example); // true
}
example();

// 普通对象没有 callee 属性
var obj = { length: 3, 0: "a", 1: "b", 2: "c" };
console.log("callee" in obj); // false
```

#### 3.3 确认 callee 属性不能被遍历

```js
!propertyIsEnumerable.call(value, "callee");
```

真正的 arguments 对象的 callee 属性是不能被 for...in 遍历的：

```js
// 原生 arguments 对象的 callee 属性不可枚举
function test() {
  for (var prop in arguments) {
    console.log(prop); // 不会输出 'callee'
  }
  console.log(Object.propertyIsEnumerable.call(arguments, "callee")); // false
}
test(1, 2, 3);

// 模拟的类 arguments 对象可能有可枚举的 callee
var fakeArgs = { length: 3, 0: "a", 1: "b", 2: "c", callee: function () {} };
console.log(Object.propertyIsEnumerable.call(fakeArgs, "callee")); // true
```

## 总结

isArguments 它用了两种方式来识别 arguments 对象：一种是看对象的标签，另一种是看对象的特征。这样不管在什么环境下都能准确判断。既保证了兼容性，又没有变得很复杂。
