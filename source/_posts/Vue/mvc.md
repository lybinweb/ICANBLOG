---
title: 随手基于MVC思想实现计算器案例
date: 2022 0818
tags:
  - MVC
  - 计算器案例
categories:
  - Vue
description: MVC
abbrlink: 5645c7b1
---

## 随手基于MVC思想实现计算器案例

### 后端MVC

- M：Model 数据模型 -> 操作数据库（对数据进行增删改查）
- V：View     视图层 		显示视图与视图模板
- C：Controller 
	- 服务端渲染：
    - 控制层、逻辑层 ：	  数据和视图关联挂载和基本的逻辑操作
    - View需要数据 	Controller对应的方法	调用Model的方法	获取数据	返回给Controller对应的方法	render到View中
  - 前端渲染：
    - API层 ：前端请求的API对应的是控制器中的方法
    - 前端	异步请求URL	控制器中的一个方法	Model层的方法	操作数据库	获取数据	返回给控制器方法	响应回前端

### 前端MVC

- Model：管理视图所需要的数据 数据与视图的关联
- View：HTML模板 + 视图渲染
- Controller：管理事件逻辑

### 基于MVC思想实现计算器案例

```html
<div id="app"></div>
```


```js
(function() {

  function init() {
    model.init(); // 组织数据 数据监听操作&数据代理
    view.render(); // 组织HTML模板 渲染HTML模板
    controller.init(); // 事件处理函数绑定
  }

  var model = {
    data: {
      a: 1,
      b: 2,
      s: '+',
      r: 0
    },
    init: function() {
      var _this = this;

      for(var key in _this.data) {

        (function(k){
          Object.defineProperty(_this, k, {
            get: function() {
              return _this.data[k];
            },

            set: function(newValue) {
              _this.data[k] = newValue;
              view.render({ [k]: newValue}); 
            }
          });
        })(key);
      }
    }
  }

  var view = {
    el: '#app',
    template: `
      <p>
        <span class="cal-a">{{ a }}</span>
        <span class="cal-s">{{ s }}</span>
        <span class="cal-b">{{ b }}</span>
        <span>=</span>
        <span class="cal-r">{{ r }}</span>
      </p>
      <p>
        <input type="text" placeholder="Number a" class="cal-input a" />
        <input type="text" placeholder="Number b" class="cal-input b" />
      </p>
      <p>
        <button class="cal-btn">+</button>
        <button class="cal-btn">-</button>
        <button class="cal-btn">*</button>
        <button class="cal-btn">/</button>
      </p>
    `,
    render: function(mutedData) {
      if(!mutedData) {
        this.template = this.template.replace(
          /\{\{(.*?)\}\}/g,
          function(node, key) {
            return model[key.trim()];
          }
        );

        var container = document.createElement('div');
        container.innerHTML = this.template;
        document.querySelector(this.el).appendChild(container);
      }else {
        for(var key in mutedData) {
          document.querySelector('.cal-' + key).textContent = mutedData[key];
        }
      }
    }
  }

  var controller = {
    init: function() {
      var oCalInputs = document.querySelectorAll('.cal-input'),
          oCalBtns = document.querySelectorAll('.cal-btn'),
          inputItem,
          btnItem;

      for(var i = 0; i < oCalInputs.length; i ++) {
        inputItem = oCalInputs[i];

        inputItem.addEventListener('input', this.handleInput, false);
      }

      for(var i = 0; i < oCalBtns.length; i ++) {
        btnItem = oCalBtns[i];

        btnItem.addEventListener('click', this.handleBtn, false);
      }
    },
    handleInput: function(e) {
      var tar = e.target,
          value = Number(tar.value),
          field = tar.className.split(' ')[1];

      model[field] = value;

      // model.r = eval('model.a' + model.s + 'model.b');
      with(model) {
        r = eval('a' + s + 'b');
      }
    },
    handleBtn: function(e) {
      var type = e.target.textContent;

      model.s = type;

      with(model) {
        r = eval('a' + s + 'b');
      }
    }
  }

  init();
})();
```