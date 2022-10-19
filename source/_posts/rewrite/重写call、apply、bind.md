---
title: 重写call、apply、bind
tags:
  - JavaScript
  - 重写
  - rewrite
categories:
  - JavaScript
description: 重写call、apply、bind
date: 2022-10-19 10:00:00
updated: 2022-10-19 10:00:00
---

## 重写call

```js
Function.prototype.myCall = function(ctx) {
	// 1. ctx上下文必须是一个对象 否则没有意义
	ctx = ctx ? Object(ctx) : window;

	// 2. 将this指向改为ctx上下文 谁调用指向谁
	// 		originFn跟test都指向了同一个内存地址
	ctx.originFn = this;

	var args = [];

	// 3. 拿到test的第二个参数以后的所有参数 作为test的实参
	for(var i = 1; i < arguments.length; i ++) {
		// 4. 利用ctx.originFn的执行（并且把获取到的实参放入），将this改变
		// 将实参以字符串形式存起来，以便于函数的执行
		args.push('arguments[' + i + ']'); // args: ['arguments[1]', 'arguments[2]']
	}

	// 5. 执行字符串函数（字符串跟数组拼接，数组会自动toString为字符串）
	eval('ctx.originFn(' + args + ')');
	// 6. 将ctx上下文中的临时变量删除
	delete ctx.originFn;
}

function test() {
	console.log(this, arguments);
}

test.myCall({
	a: 1,
	b: 2
}, '张三', '李四')
```

## 重写apply

```js
Function.prototype.myApply = function(ctx, args) {
	ctx = ctx ? Object(ctx) : window;
	ctx.originFn = this;

	// 如果第二个参数为原始值会抛出异常
	if(typeof(args) !== 'object' && typeof(args) !== 'function') {
		throw new TypeError('CreateListFromArrayLike called on non-object');
	}

	// 如果第二个参数没有传或者不是数组，那么就直接执行
	if(!args || typeOf(args) !== 'array') {
		return ctx.originFn();
	}

	// 将第二个参数平铺到originFn中利用eval执行
	var ret = eval('ctx.originFn('+ args +')');
	delete ctx.originFn;

	return ret;
}

function typeOf(value) {
	if(value === null) return 'null';

	return typeof(value) === 'object' ? {
		'[object Object]': 'object',
		'[object Array]': 'array',
		'[object Number]': 'object-number',
		'[object String]': 'object-string', 
		'[object Boolean]': 'object-boolean',
	}[({}).toString.call(value)] : typeof(value);
}
```

## 重写bind

```js
/**
 * 1. test.bind() 相当于 test执行
 * 2. bind的第一个参数是test的this指向
 * 3. bind可以分离test参数 因为bind执行返回一个新的函数 
 * 		可以在bind传一部分参数跟在返回的新函数中传另外一部分参数
 * 4. bind跟call参数传递是一样的
 * 5. 实例化返回的函数this指向test构造出来的实例
 * 6. 实例应该继承构造函数的原型属性
 */
Function.prototype.myBind = function(ctx) {
	var originFn = this,
			args = [].slice.call(arguments, 1), // 截取第二个参数以后的参数
			_tempFn = function() {}; // 临时缓存池

	// 返回一个新的函数
	var fn = function() {
		var newArgs = [].slice.call(arguments); // 保存新函数的参数

		return originFn.apply(
			this instanceof fn ? this : ctx, // 如果实例化了新函数那么this指向该新实例 否则 指向ctx上下文
			args.concat(newArgs)); // 拼接两个参数列表
	}

	// 将test的prototype交给中介
	_tempFn.prototype = this.prototype;
	// 将中介的实例化对象交给fn的原型属性
	fn.prototype = new _tempFn();

	return fn;
}
```