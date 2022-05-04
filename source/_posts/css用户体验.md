---
title: css用户体验
date: 2022-05-04 19:12:50
tags:
  - css
  - css揭秘
categories:
  - css
cover: /img/css-secret.jpg
---

## 选择适合的鼠标光标
### 禁用光标
```css
cursor: not-allowed;
```

### 隐藏光标
```css
cursor: url('transparent.gif');
cursor: none;
```

## 扩大可点击区域
伪元素：
```css
button {
  position: relative;
}

button::before {
  content: '';
  position: absolute;
  top: -10px;
  left: -10px;
  right: -10px;
  bottom: -10px;
}
```

## 自定义复选框
<style>
  #awesome {
    position: absolute;
    clip: rect(0, 0, 0, 0);
  }
  #awesome + label {
    cursor: pointer;
  }
  #awesome + label::before {
    content: '\a0';
    display: inline-block;
    vertical-align: .2em;
    width: 1em;
    height: 1em;
    margin-right: .2em;
    border-radius: .2em;
    background: silver;
    text-indent: .15em;
    line-height: .65;
    cursor: pointer;
  }
  #awesome:checked + label::before {
    content: '\2713';
    background: yellowgreen;
  }
  #awesome:hover + label::before {
    box-shadow: 0 0 .1em .1em #58a;
  }
</style>

<input type="checkbox" id="awesome"/>
<label for="awesome">awesome</label>

### 自定义复选框
```html
<input type="checkbox" id="awesome"/>
<label for="awesome">awesome</label>
```

```css
input[type="checkbox"] {
  position: absolute;
  clip: rect(0, 0, 0, 0);
}
input[type="checkbox"] + label {
  cursor: pointer;
}
input[type="checkbox"] + label::before {
  content: '\a0';
  display: inline-block;
  vertical-align: .2em;
  width: 1em;
  height: 1em;
  margin-right: .2em;
  border-radius: .2em;
  background: silver;
  text-indent: .15em;
  line-height: .65;
  cursor: pointer;
}
input[type="checkbox"]:checked + label::before {
  content: '\2713';
  background: yellowgreen;
}
input[type="checkbox"]:hover + label::before {
  box-shadow: 0 0 .1em .1em #58a;
}
```
### 开关式按钮
<style>
#awesome1 {
	position: absolute;
	clip: rect(0,0,0,0);
}

#awesome1 + label {
	display: inline-block;
	padding: .35em .5em .2em;
	background: #ccc;
	background-image: linear-gradient(#ddd, #bbb);
	border: 1px solid rgba(0,0,0,.2);
	border-radius: .3em;
	box-shadow: 0 1px white inset;
	text-align: center;
	text-shadow: 0 1px 1px white;
	cursor: pointer;
  margin-bottom: 10px;
}

#awesome1:checked + label,
#awesome1:active + label {
	box-shadow: .04em .1em .2em rgba(0,0,0,.6) inset;
	border-color: rgba(0,0,0,.3);
	background: #bbb;
}
</style>

<input type="checkbox" id="awesome1"/>
<label for="awesome1">awesome</label>

```html
<input type="checkbox" id="awesome1"/>
<label for="awesome1">awesome</label>
```

```css
#awesome1 {
	position: absolute;
	clip: rect(0,0,0,0);
}

#awesome1 + label {
	display: inline-block;
	padding: .35em .5em .2em;
	background: #ccc;
	background-image: linear-gradient(#ddd, #bbb);
	border: 1px solid rgba(0,0,0,.2);
	border-radius: .3em;
	box-shadow: 0 1px white inset;
	text-align: center;
	text-shadow: 0 1px 1px white;
	cursor: pointer;
}

#awesome1:checked + label,
#awesome1:active + label {
	box-shadow: .04em .1em .2em rgba(0,0,0,.6) inset;
	border-color: rgba(0,0,0,.3);
	background: #bbb;
}
```

## 通过阴影弱化背景
### 遮罩层
```css
.overlay {
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, .8);
}
.dialog {
  position: absolute;
  z-index: 1;
}
```

### 伪元素方案
```css
.dialog::before {
  content: '';
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, .8);
  z-index: -1;
}
```

存在问题：
点击背景层关闭，对于这种实现起来比较麻烦

### box-shadow方案
```css
box-shadow: 0 0 0 50vmax rgba(0,0,0,.8);
position: fixed; // 为了防止滚动页面时遮罩层弹出
```

存在问题：
box-shadow 只是能引起视觉上的效果 无法阻止鼠标交互（弹出层外背景中的元素仍能被点击）

### backdrop方案
```css
dialog::backdrop {
  backgroud: rgba(0,0,0,.8)
}
```

存在问题：
浏览器不完全支持

## 通过模糊来弱化背景
```css
main.de-emphasized {
  filter: blur(5px);
}
```

模糊和阴影结合
```css
main.de-emphasized {
  filter: blur(5px) contrast(.8px) brightness(.8);
}
```
如果浏览器不支持，看不到任何效果，最好加上box-shadow作为浏览器回退方案。

如果还需支持鼠标在该背景区域上无法交互，使用遮罩层+filter的方式。

## 滚动提示
<style>
  .example ul {
    border: 1px solid black;
    width: 200px;
    height: 150px;
    overflow: auto;
    background: linear-gradient(white 15px, transparent) 0 0 / 100% 50px,
                radial-gradient(at top, rgba(0,0,0,.2), transparent 70%) 0 0 / 100% 30px,
                linear-gradient(to top, white 15px, transparent) bottom / 100% 50px,
                radial-gradient(at bottom, rgba(0,0,0,.2), transparent 70%) bottom / 100% 30px;
    background-repeat: no-repeat;
    background-attachment: local, scroll, local, scroll;
  }
  .example ul li {
    display: inline-block;
    height: 50px;
  }
</style>

在移动端用的比较多，支持向上滚动头部显示“提示”，支持向下滚动底部显示“提示”
<div class="example">
  <ul>
    <li>Ada Catlace</li>
    <li>Alan Purring</li>
    <li>Schrödingcat</li>
    <li>Tim Purrners-</li>
    <li>Lee</li>
    <li>Webkitty</li>
    <li>json</li>
    <li>void</li>
    <li>Tim Purrners-</li>
    <li>Lee</li>
    <li>Webkitty</li>
    <li>json</li>
    <li>void</li>
  </ul>
</div>

代码可参考：https://dabblet.com/gist/20205b5fcdd834461e80

## 交互式的图片对比控件

### css Resize 方案

<div class="image-slider">
  <div>
    <img src="/img/cat.jpg" alter="before"/>
  </div>
  <img src="/img/cat.jpg" alter="after"/>
</div>

<style>
.image-slider {
  position: relative;
  display: inline-block;
}
.image-slider > div {
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 50%;
  height: 100%;
  overflow: hidden;
  resize: horizontal;
  max-width: 100%;
}
.image-slider > div img {
  display: block;
  filter: blur(10px);
}
img {
  width: 769.2px;
  height: 513.05px;
  margin: 0 !important;
  max-width: none !important;
}
</style>

```html
<div class="image-slider">
  <div>
    <img src="picture1.jpg" alter="before"/>
  </div>
  <img src="picture2.jpg" alter="after"/>
</div>
```

```css
.image-slider {
  position: relative;
  display: inline-block;
}
.image-slider > div {
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 50%;
  height: 100%;
  overflow: hidden;
  resize: horizontal;
  max-width: 100%; // 防止拉动调节手柄后超出图片大小
}
```

除此之外，还能通过伪元素改变调节手柄的大小
```css
.image-slider > div::before {
  content: '';
  position: absolute;
  right: 0;
  bottom: 0;
  width: 12px;
  height: 12px;
  padding: 5px;
  background:
    linear-gradient(-45deg, white 50%, transparent);
  background-clip: content-box;
  cursor: ew-resize;
}
.image-slider img {
  display: block;
  user-select: none; // 防止没有点击调节手柄 拖动鼠标 误选图片的情况
}
```

### 范围输入控件方案
<input type="range"/>

```html
<input type="range"/>
```
然后监听Input的值，动态改变第一张图片的宽度。略。
比较推荐这种方案，浏览器支持度更高，不过需要编写额外的js代码。