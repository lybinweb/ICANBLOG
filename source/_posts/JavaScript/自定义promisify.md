---
title: promisify函数封装
tags:
  - JavaScript
  - Promise
categories:
  - JavaScript
description: promisify函数
date: 2022-09-08 14:00:00
---

## promisify函数的封装

```js
function promisify(fn) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      fn(...args, (err, data) => {
        if(err) {
          reject(err);
        }else {
          resolve(data);
        }
      })
    })
  }
}

```

> 在node中已经封装到了util中


- 使用：

```js
let readFile = promisify(fs.readFile);

readFile('./name.txt', 'utf-8')
  .then(data => readFile(data, 'utf-8'))
  .then(data => readFile(data, 'utf-8'))
  .then(data => console.log(data));
```

## promisifyAll的封装

> 比如说把node中fs模块下的所有函数都通过promisify化

```js
function promisifyAll(obj) {
  for(var [key, fn] of Object.entries(obj)) {
    if(typeof fn === 'function') {
      obj[key + 'Async'] = promisify(fn);
    }
  }
}

// 使用该方法
promisifyAll(fs);
fs.readFileAsync('./name.txt', 'utf-8')
	.then(data => fs.readFileSync(data, 'utf-8'))
	.then(data => fs.readFileSync(data, 'utf-8'))
	.then(data => console.log(data));
```