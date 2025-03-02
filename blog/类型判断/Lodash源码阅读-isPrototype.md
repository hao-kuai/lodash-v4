---
highlight: tomorrow-night-bright
theme: vue-pro
---

## 功能概述

isPrototype 函数是 Lodash 中用于检测一个值是否为某个对象的原型（prototype）的工具函数。它通过比较对象的构造函数的原型来判断，帮助我们识别一个对象是否是作为其他对象的原型。

## 源码实现

```js
function isPrototype(value) {
  var Ctor = value && value.constructor,
    proto = (typeof Ctor == "function" && Ctor.prototype) || objectProto;

  return value === proto;
}
```

## 实现原理解析

### 1. 获取构造函数

```js
var Ctor = value && value.constructor;
```

这行代码做了两件事：

1. 首先通过 `value && value.constructor` 进行短路判断：
   - 如果 value 是 null 或 undefined，直接返回 null（短路）
   - 如果 value 有效，则获取其构造函数

示例：

```js
// 自定义类的情况
class Person {}
const personProto = Person.prototype;
console.log(personProto.constructor === Person); // true

// 内置对象的情况
const arrayProto = Array.prototype;
console.log(arrayProto.constructor === Array); // true

// null 的情况
const nullValue = null;
console.log(nullValue && nullValue.constructor); // null
```

### 2. 获取原型对象

```js
proto = (typeof Ctor == "function" && Ctor.prototype) || objectProto;
```

这行代码也很巧妙：

1. 先判断 `typeof Ctor == 'function'`：

   - 确保构造函数真的是个函数
   - 如果不是函数，这部分表达式返回 false

2. 如果是函数，就获取 `Ctor.prototype`：

   - 每个函数都有一个 prototype 属性
   - 这个属性指向该函数作为构造函数时的原型对象

3. 如果前面都失败了，就用 `objectProto`（Object.prototype）作为后备：
   - 这确保了即使处理异常情况也有一个有效的原型对象

示例：

```js
// 自定义类的原型
class Animal {}
const animalProto = Animal.prototype;
console.log(Animal.prototype === animalProto); // true

// 内置对象的原型
console.log(Array.prototype === [].__proto__); // true
console.log(Object.prototype === {}.__proto__); // true

// 非函数构造器的情况
const obj = { constructor: 42 };
const proto =
  (typeof obj.constructor === "function" && obj.constructor.prototype) ||
  Object.prototype;
console.log(proto === Object.prototype); // true
```

### 3. 最终比较

```js
return value === proto;
```

这是最后的判断，使用严格相等（===）来比较：

- 检查传入的 value 是否就是刚才获取到的原型对象
- 使用 === 确保是同一个对象引用，而不是具有相同属性的不同对象

示例：

```js
// 检查是否是 Array 的原型
console.log(isPrototype(Array.prototype)); // true

// 检查普通对象是否是原型
const normalObj = { x: 1 };
console.log(isPrototype(normalObj)); // false

// 检查自定义类的原型
class MyClass {}
console.log(isPrototype(MyClass.prototype)); // true

// 特殊情况：Object.prototype
console.log(isPrototype(Object.prototype)); // true
```

## 注意事项

1. 这个函数主要用于内部实现，一般用户代码很少直接使用
2. 不要用这个函数来判断对象之间的继承关系，应该使用 instanceof 运算符

```js
// 错误的继承关系判断
class Animal {}
class Dog extends Animal {}

const dog = new Dog();
console.log(isPrototype(Animal.prototype)); // false，不能用这个判断 dog 是否继承自 Animal

// 正确的继承关系判断
console.log(dog instanceof Animal); // true，使用 instanceof 判断继承关系
console.log(Object.prototype.isPrototypeOf(dog)); // true，或使用 isPrototypeOf 方法
```

3. 函数返回 true 的情况很少，因为一个对象要完全等于构造函数的 prototype 属性才行

```js
// 返回 true 的情况
console.log(isPrototype(Object.prototype)); // true
console.log(isPrototype(Array.prototype)); // true
console.log(isPrototype(Function.prototype)); // true

// 返回 false 的情况
const proto = { x: 1 };
function CustomClass() {}
CustomClass.prototype = proto;

console.log(isPrototype(new CustomClass())); // false，实例对象不是原型
console.log(isPrototype(proto)); // false，虽然是原型但不是构造函数直接的 prototype

// 特殊情况：Object.create(null) 创建的对象
const noProtoObj = Object.create(null);
console.log(isPrototype(noProtoObj)); // false，没有原型链的对象
```

## 总结

函数的判断逻辑是：

1. 先获取这个对象的构造函数（value.constructor）
2. 再获取构造函数的原型对象（constructor.prototype）
3. 最后看传入的对象是否就是这个原型对象
