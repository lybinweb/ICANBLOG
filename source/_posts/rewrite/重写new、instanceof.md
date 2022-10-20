---
title: 重写new、instanceof
tags:
  - JavaScript
  - 重写
  - rewrite
categories:
  - JavaScript
description: 重写new、instanceof
date: 2022-10-20 14:52:00
updated: 2022-10-20 14:52:00
---

## 重写new

```js
// 1. 创建构造函数

// 2. 实例化构造函数
  /*
    1. this -> {}
    2. this.a this.b
    3. { a: a, b: b, __proto__: C.prototype{ constructor: C } }
  */
// 3. 接收构造函数中的返回值


var test = myNew(Test, 1, 2);

function myNew() {
	// 返回arguments里的第一个参数 并且从arguments中删除第一个参数
	var constructor = [].shift.call(arguments);
	var _this = {};
	
	_this.__proto__ = constructor.prototype;
	var res = constructor.apply(_this, arguments);
	
	return typeOf(res) === 'object' ? res : _this;
	
	return _this;
}
```

## 重写instanceof

```js
// 前提
var arr = [];
console.log(arr instanceof Array); // true
console.log(arr instanceof Object); // true

arr.__proto__ === Array.prototype; // true
arr.__proto__ === Object.prototype; // false

Object.prototype.__proto__ === null; // true
arr.__proto__.__proto__.__proto__ === null; // true


function instanceOf(target, type) {
	type = type.prototype;
	target = target.__proto__;
	
	while(true) {
		if(target === null) {
			return false;
		}
		
		if(target === type) {
			return true;
		}
		
		target = target.__proto__;
	}
}
```