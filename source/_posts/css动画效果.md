---
title: css动画效果
date: 2022-06-22 22:10:09
tags:
  - css
  - css揭秘
categories:
  - css
cover: /img/css-secret.jpg
mathjax: true
---

## 缓动动画

### 小球下落效果

<div class="example ball-wrap">
  <div class="ball"></div>
</div>

<style>
  @keyframes bounce {
    60%, 80%, 100% {
      transform: translateY(80px);
      animation-timing-function: cubic-bezier(.215, .61, .355, 1);
    }
    70% {
      transform: translateY(60px);
    }
    90% {
      transform: translateY(70px);
    }
  }

  .example {
    margin-bottom: 20px;
  }
  .ball-wrap {
    width: 50px;height:100px;
    border: 1px solid black;
  }
  .ball {
    width: 20px;height:20px;background:orange;border-radius:50%;margin: 0 auto;
    animation: bounce 3s cubic-bezier(.755, .05, .855, .06) infinite;
  }
</style>

```css
@keyframes bounce {
  60%, 80%, 100% {
    transform: translateY(80px);
    animation-timing-function: cubic-bezier(.215, .61, .355, 1);
  }
  70% {
    transform: translateY(60px);
  }
  90% {
    transform: translateY(70px);
  }
}

.ball {
  width: 20px;height:20px;background:orange;border-radius:50%;margin: 0 auto;
  animation: bounce 3s cubic-bezier(.755, .05, .855, .06) infinite;
}
```

三次贝塞尔曲线可视化：https://cubic-bezier.com

### 输入框提示效果
<style>
  .input-wrap {
    height: 24px;
    position: relative;
  }
  .input-wrap .callout {
    background: pink;
    position: absolute;
    left: 185px;
    top: -20px;
    transform: scale(0);
    transition: .25s transform;
  }
  .input-wrap input:focus + .callout{
    transition: .5s cubic-bezier(.25, .1, .3, 1.5) transform;
    transform: scale(1);
  }
</style>
<div class="input-wrap example">
  <input></input>
  <div class="callout">一些提示，<br/>比如只能输入数字</div>
</div>


```html
<div class="input-wrap">
  <input></input>
  <div class="callout">一些提示，<br/>比如只能输入数字</div>
</div>
```
```css
.input-wrap {
  height: 24px;
  position: relative;
}
.input-wrap .callout {
  background: pink;
  position: absolute;
  left: 185px;
  top: 0;
  transform: scale(0);
  transition: .25s transform;
}
.input-wrap input:focus + .callout{
  transition: .5s cubic-bezier(.25, .1, .3, 1.5) transform;
  transform: scale(1);
}
```

## 逐帧动画

### loading效果
网上随便找的一个马跑步素材：
<style>
  @keyframes run {
    to {
      background-position: -550px;
    }
  }
.horse {
  width: 140px;
  height: 140px;
  background: url('/img/horse2.jpg');
  animation: run 4s steps(4) infinite;
}
</style>
<div class="horse example">
</div>

核心原理：background-position和steps

如果Loading图片为100x100,且有8个loading条：
```css
@keyframes loading {
  from {
    background-position: 0;
  }
  to {
    background-position: -800px 0;
  }
}
```
```css
animation: loading 1s steps(8) infinite;
```

### 闪烁效果
<style>
  @keyframes blink {
    50% { background: transparent; }
  }
  .blink-example {
    width: 1px;
    height: 20px;
    background: black;
    animation: blink 1s steps(1) infinite;
  }
</style>
<div class="blink-example"></div>

```css
animation: blink 1s steps(1) infinite;
```

## 打字动画
<style>
  @keyframes typing {
    from {
      width: 0;
    }
  }
  @keyframes caret {
    50% { border-color: transparent; }
  }
  .css-wrap {
    width: 15ch;
    overflow: hidden;
    white-space: nowrap;
    animation: typing 15s steps(15) infinite, caret 1s steps(1) infinite;
    border-right: .05em solid;
  }
</style>
<div class="css-wrap">
CSS is awesome!
</div>
<br/>
<div class="css-wrap">123456789123456</div>
<br/>

打字动画实际上是文字逐个显示动画+光标闪烁动画
```css
@keyframes typing {
  from {
    width: 0;
  }
}
h1 {
  width: 15ch;
  overflow: hidden;
  white-space: nowrap;
  animation: typing 15s steps(15);
}
```
```css
animation: typing 15s steps(15);
```

## 状态平滑的动画
```css
animation-play-state: pause;
```

## 沿环形路径平移的动画
### 方案一
用内层的变形来抵消外层的变形效果。

<style>
@keyframes spin {
  to {
    transform: rotate(1turn);
  }
}
.path {
  width: 100px;
  height: 100px;
  background: orange;
  border-radius: 50%;
}
.avatar {
  width: 20px;
  height: 20px;
  margin: 0 auto;
  border-radius: 50%;
  overflow: hidden;
  animation: spin 3s infinite linear;
  transform-origin: 50% 50px;  /* 50px 是背景圆形的半径 */
}
.avatar img {
  animation: spin 3s infinite linear;
  animation-direction: reverse;
}
</style>

<div class="path example">
  <div class="avatar">
    <img src="/img/avatar.jpg">
  </div>
</div>

```html
<div class="path">
  <div class="avatar">
    <img src="/img/avatar.jpg">
  </div>
</div>
```
```css
@keyframes spin {
  to {
    transform: rotate(1turn);
  }
}
.path {
  width: 100px;
  height: 100px;
  background: orange;
  border-radius: 50%;
}
.avatar {
  width: 20px;
  height: 20px;
  margin: 0 auto;
  border-radius: 50%;
  overflow: hidden;
  animation: spin 3s infinite linear;
  transform-origin: 50% 50px;  /* 50px 是背景圆形的半径 */
}
.avatar img {
  animation: inherit;
  animation-direction: reverse;
}
```

### 方案二
使用translate模拟transform-origin，从而不需要在Img外层多嵌套一个元素。

<style>
@keyframes spin2 {
  from {
    transform: translateY(50px)
              rotate(0turn)
              translateY(-50px)
              translateY(50%)
              rotate(1turn)
              translateY(-50%)
  }
  to {
    transform: translateY(50px)
              rotate(1turn)
              translateY(-50px)
              translateY(50%)
              rotate(0turn)
              translateY(-50%)
  }
}
@keyframes spin3 {
  from {
    transform: rotate(0turn)
              translateY(-50px)
              translateY(50%)
              rotate(1turn)
  }
  to {
    transform: rotate(1turn)
              translateY(-50px)
              translateY(50%)
              rotate(0turn)
  }
}
.img {
  width: 20px;
  height: 20px;
  margin: 0 auto;
  border-radius: 50%;
  animation: spin2 3s infinite;
}
.img2 {
  width: 20px;
  height: 20px;
  position: relative;
  top: 40px;
  border-radius: 50%;
  animation: spin3 3s infinite;
}
</style>
<div class="path example">
  <img src="/img/avatar.jpg" class="img">
</div>

```html
<div class="path">
  <img src="/img/avatar.jpg">
</div>
```

```css
@keyframes spin2 {
  from {
    transform: translateY(50px)
              rotate(0turn)
              translateY(-50px)
              translateY(50%)
              rotate(1turn)
              translateY(-50%)
  }
  to {
    transform: translateY(50px)
              rotate(1turn)
              translateY(-50px)
              translateY(50%)
              rotate(0turn)
              translateY(-50%)
  }
}
```
如果一开始头像就位于圆心，上面的css代码可简化：
也就是把translateY(50px) translateY(-50%)去除，这两句做的实际上就是把头像移到圆心。
```css
@keyframes spin2 {
  from {
    transform: rotate(0turn)
              translateY(-50px)
              translateY(50%)
              rotate(1turn)
  }
  to {
    transform: rotate(1turn)
              translateY(-50px)
              translateY(50%)
              rotate(0turn)
  }
}
```