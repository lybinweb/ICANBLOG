---
title: defineProperty版计算器
tags:
  - JavaScript
  - Object方法
  - Object.defineProperty
  - 数据劫持
categories:
  - JavaScript
description: defineProperty版计算器
date: 2022-09-20 17:06:00
---

## 数据劫持版计算器

![jVE9t.gif](https://www.giaott.com/images/2022/09/20/jVE9t.gif)

### HTML结构

```html
<div class="J_calculator">
	<div class="result">0</div>
	<div class="input-group">
		<input type="text" value="0" class="f-input" />
	</div>
		<input type="text" value="0" class="s-input" />
	<div class="btn-group">
		<button data-field="plus" class="current">+</button>
		<button data-field="minus">-</button>
		<button data-field="mul">*</button>
		<button data-field="div">/</button>
	</div>
</div>
```

### CSS样式

```css
.current {
  background: orange;
}
```

### JavaScript

```js

class Compute {
	plus(a, b) {
		return a + b;
	}

	minus(a, b) {
		return a - b;
	}

	mul(a, b) {
		return a * b;
	}

	div(a, b) {
		return a / b;
	}
}

class Calculator extends Compute{
	constructor(doc) {
		super();

		const oCal = doc.getElementsByClassName('J_calculator')[0];

		this.fInput = oCal.getElementsByTagName('input')[0];
   	this.sInput = oCal.getElementsByTagName('input')[1];

   	this.oBtnGroup = oCal.getElementsByClassName('btn-group')[0];
   	this.oBtnItems = this.oBtnGroup.getElementsByTagName('button');

   	this.oResult = oCal.getElementsByClassName('result')[0];

   	// 所用到的数据
   	this.data = this.defineData();
   	// 用来标识当前的运算符下标
   	this.btnIdx = 0;

   	// console.log(this.data);
	}

	defineData() {
		let _obj = {},
				fNumber = 0,
				sNumber = 0,
				field = 'plus';

		const _self = this;

		Object.defineProperties(_obj, {
			fNumber: {
				get() {
					console.log('"fNumber" is being got.')
					return fNumber;
				},
				set(newVal) {
					fNumber = newVal;
					_self.computeResult(fNumber, sNumber, field);
					console.log(`The value "fNumber" has been changed.[${newVal}]`)
					
				}
			},

			sNumber: {
				get() {					
					console.log('"sNumber" is being got.')
					return sNumber;
				},
				set(newVal) {
					sNumber = newVal;
					_self.computeResult(fNumber, sNumber, field);
					console.log(`The value "sNumber" has been changed.[${newVal}]`)
				}
			},

			field: {
				get() {
					console.log('"field" is being got.')
					return field;
				},
				set(newVal) {
					field = newVal;
					_self.computeResult(fNumber, sNumber, field);
					console.log(`The value "field" has been changed.[${newVal}]`)
				}
			},
		});

		return _obj;
	}

	computeResult(fNumber, sNumber, field) {
		this.oResult.innerText = this[field](fNumber, sNumber);
	}

	init() {
		this.bindEvent();
	}

	bindEvent() {
		// 事件代理 给button的父级绑定点击事件
		this.oBtnGroup.addEventListener('click', this.onFieldBtnClick.bind(this), false);
		// 给input绑定事件
		this.fInput.addEventListener('input', this.onNumberInput.bind(this), false);
		this.sInput.addEventListener('input', this.onNumberInput.bind(this), false);
	}

	onFieldBtnClick(ev) {
		const e = ev || window.event,
					tar = e.target || e.srcElement,
					tarName = tar.tagName.toLowerCase();

		tarName === 'button' && this.fieldUpdate(tar);

	}

	onNumberInput(ev) {
		const e = ev || window.event,
					tar = e.target || e.srcElement,
					className = tar.className,
					val = Number(tar.value.replace(/\s+/g, '')) || 0;

		switch(className) {
			case 'f-input':
				this.data.fNumber = val;
				break;
			case 's-input':
				this.data.sNumber = val;
				break;
			default:
				break;
		}
	}

	fieldUpdate(tar) {
		// 清空点击之前的btn类名
		this.oBtnItems[this.btnIdx].className = '';
		// 改变当前的btn下标 使用indexOf方法判断当前点击的元素tar是否在btn集合中，并返回索引
		this.btnIdx = [].indexOf.call(this.oBtnItems, tar);
		// 给点击的btn增加聚焦类名 
		tar.className += ' current';
		// 设置数据
		this.data.field = tar.getAttribute('data-field');
	}
}

new Calculator(document).init();
```