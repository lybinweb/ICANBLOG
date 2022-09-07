---
title: 重写typeof
tags:
  - JavaScript
  - 重写
  - rewrite
categories:
  - JavaScript
description: 重写typeof
abbrlink: 7b8bf196
---

## typeof方法

- MDN解释：返回一个字符串，表示未经计算的操作数的类型

- 语法：
  - typeof operand
  - typeof(operand)

- 参数：一个表示对象或原始值的表达式，并将其数据类型返回

- 返回值：返回一个数据类型的字符串

- typeof有可能返回的值：
  1. 'string'
  2. 'function'
  	- typeof Object
  3. 'number'
  4. 'string'
  5. 'undefined'
  6. 'object'
  	- typeof {}
  	- typeof []
  	- typeof null
  	- typeof new String
  	- typeof new Boolean
  	- typeof new Number

## 重写typeof

```js
function typeOf(value) {
  if(value === null) return null;

  return typeof(value) === 'object' ? {
		'[object Object]': 'Object',
		'[object Array]': 'Array',
		'[object Number]': 'o-Number',
		'[object String]': 'o-String',
		'[object Boolean]': 'o-Boolean'
  }[({}).prototype.toString.call(value)]
                           : typeof(value);
}
```