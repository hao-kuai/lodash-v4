## 功能概述

isError 函数是 Lodash 中用于判断一个值是否为错误对象的工具函数。它不仅能识别标准的 Error 实例，还支持 DOM 异常对象和符合错误对象特征的自定义对象，是 Lodash 类型检测体系中处理错误类型的专用方法。

## 源码实现

```js
function isError(value) {
  if (!isObjectLike(value)) {
    return false;
  }
  var tag = baseGetTag(value);
  return (
    tag == errorTag ||
    tag == domExcTag ||
    (typeof value.message == "string" &&
      typeof value.name == "string" &&
      !isPlainObject(value))
  );
}
```

## 实现原理解析

### 1. 对象类型预检

```js
if (!isObjectLike(value)) {
  return false;
}
```

- 使用 isObjectLike 进行初步类型检查
- 过滤掉原始类型值和 null
- 确保待检测值是类对象结构

```js
isError(123); // false（非对象）
isError("Error"); // false（非对象）
isError([]); // false（类数组但非错误对象）
```

### 2. 内部标签检测

```js
var tag = baseGetTag(value);
return tag == errorTag || tag == domExcTag;
```

- 通过 baseGetTag 获取对象的内部标签
- errorTag 对应 '[object Error]'，用于匹配标准 Error 实例
- domExcTag 对应 '[object DOMException]'，用于匹配浏览器 DOM 异常

```js
isError(new Error("逻辑错误")); // true（标准Error对象）
isError(new TypeError("类型错误")); // true（Error子类实例）
isError(new DOMException("非法访问")); // true（DOM标准错误）
```

### 3. 错误对象特征验证

```js
typeof value.message == "string" &&
  typeof value.name == "string" &&
  !isPlainObject(value);
```

作为兜底策略，通过检查对象的特征属性来判断自定义错误对象：

- message 属性必须为字符串类型
- name 属性必须为字符串类型
- 不能是普通对象（通过 isPlainObject 排除）

```js
// 自定义错误对象
class CustomError {
  constructor(message) {
    this.name = "CustomError";
    this.message = message;
  }
}
_.isError(new CustomError("test")); // => true

// 普通对象（即使有 name 和 message）
_.isError({ name: "Error", message: "test" }); // => false
```

## 总结

isError 函数采用多层次的检测策略，通过类型标签和对象特征的组合判断，准确识别各类错误对象。其实现既考虑了标准 Error 实例和 DOM 异常的处理，又支持符合错误对象特征的自定义对象。
