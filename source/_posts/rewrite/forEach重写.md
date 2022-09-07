---
title: 实现forEach
tags:
  - JavaScript
  - 重写
  - rewrite
categories:
  - JavaScript
description: 重写forEach、forEach会不会改变原数组
abbrlink: 718b0317
date: 2022-08-23 00:00:00
updated: 2022-09-07 00:00:00
---

## forEach会不会改变原数组

### 不改变原数组

```js
var arr = [1, 2, 3];

arr.forEach((item, index) => {
	item += 1;
});

console.log(arr); // [1, 2, 3]
```

### 改变原数组

```js
var arr = [1, 2, 3];

arr.forEach((item, index) => {
	arr[index] += 1;
});

console.log(arr); // [2, 3, 4]
```

## 重写forEach

```js
Array.prototype.myForEach = function(fn) {
	var arr = this,
			len = arr.length,
			_this = arguments[1] || window; // forEach的第二个参数this指向问题

	for(var i = 0; i < len; i ++) {
		fn.apply(_this, [arr[i], i, arr]);
	}
}
```