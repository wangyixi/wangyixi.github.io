---
title: JS数据类型
date: 2020-03-13 10:07:40
tags: JavaScript
---

# 基本概念

## 基本类型

基本类型可以想象成C/C++中的值类型，所谓“基本类型本身的值不会改变”，指的是基本类型在赋值过程中对原始值进行了复制，改变的是基本类型所赋值的变量。基本类型由栈自动分配内存，系统自动回收。

简单数据类型：`string`, `number`, `boolean`, `null`, `undefined`, `Symbol`(SE6)

复杂数据类型：`object`

## 引用类型

引用类型又可以称为对象，它们在赋值时，被赋值的变量和原来的变量都引用了（指向了）同一个对象。对象在堆中动态分配内存，需要垃圾回收。

引用类型（对象）：`String`, `Number`, `Boolean`, `Object`, `Function`, `Array`, `Data`, `RegExp`, `Error`

可以用以上引用类型创建相应的实例，并添加属性和方法。

# 区别

首先明确的是，JS是大小写敏感的，那么上文的 `number` 和 `Number` 当然就不同。

```javascript
var a = 1; //基本类型number
var b = Number(1); //与上面等价
var c = new Number(1); //引用类型Number，是原型对象，创建一个叫c的实例
```

![1584067175515](D:\wangyixi.github.io\source\image\1584067175515.png)

使用 `typeof` 查看变量的类型，可以看到c是一个`object`，它自动带有一个 `__proto__` 属性指向对象原型对象 `Number`

![1584067447134](D:\wangyixi.github.io\source\image\1584067447134.png)

## typeof操作符

`typeof`操作可以返回以下字符串：

`undefined` `boolean` `number` `string` `object` `function`

`null`会被理解成一个空对象。

函数`function`在JS中事实上也是一种对象，使用`typeof`区分的是函数和其他对象。

对于 `Number` 来说，使用 `Number()`创建的为 `number`类型，使用 `new NUmber()`构造函数创建的是对象，`typeof`操作符对函数名 `Number`返回的为 `function` , 由上文a,b的类型可知，`Number(1)`与1等价，所以返回的是 `number`。

正则 `RefExp`创建的变量数据类型则是`object` 。

![， 1584069399857](D:\wangyixi.github.io\source\image\1584069399857.png)

### 局限性

`typeof`操作符对未初始化和未声明的变量都会返回`undefined`值。

为了避免无法区分未初始化和未声明的变量，我们可以显式地对变量进行初始化。如果定义的变量准备将来用于保存对象，可以初始化为 `null`。

`undefined` 值派生于 `null`

# 类型转换

## 数值转换

函数`Number()` `parseInt()` `parseFloat()`可以将非数值转换成数值

## 字符串转换

除了 `null` 和 `undefined`，其他值（包括字符串）都有 `toString()`方法，该方法返回字符串的的一个副本。

在将数值转换成字符串时，可以传递一个参数指定基数。

## 对象转换

对象有一个 `valueof()` 方法，可以返回对象的字符串、数值或布尔值表示，通常与 `toString()`返回值相同。在将对象转换成数值时，先调用 `valueof`方法，如果返回 `NaN`，则调用 `toString()`