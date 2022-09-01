---
title: 实现forEach
tags:
  - JavaScript
  - 重写
  - rewrite
categories:
  - JavaScript
description: 重写forEach
abbrlink: 718b0317
---

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