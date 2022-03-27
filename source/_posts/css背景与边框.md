---
title: css背景与边框
date: 2022-03-27 15:05:51
tags:
  - css
  - css揭秘
categories:
  - css
cover: /img/css-secret.jpg
---

## 背景侵入边框问题
```css
background-clip: padding-box
```
background-clip的属性有content-box、padding-box、border-box, text, 默认为border-box,所以背景会侵入边框，改为padding-box,背景会裁剪到padding, 因而能解决背景侵入边框问题。

详细例子可在MDN上查看：https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-clip

## 多重边框问题
### background叠加
```css
 background: deeppink;
  background-image: linear-gradient(yellowgreen, yellowgreen),
                    linear-gradient(#655, #655);
  background-size: 100px 100px, 120px 120px;
  background-repeat: no-repeat, no-repeat;
  background-position: center, center;
```
代码太过复杂，不够DRY。
<div class="css-box1"></div>
<style>
.css-box1{
  width: 130px;
  height: 130px;
  background: deeppink;
  background-image: linear-gradient(yellowgreen, yellowgreen),
                    linear-gradient(#655, #655);
  background-size: 100px 100px, 120px 120px;
  background-repeat: no-repeat, no-repeat;
  background-position: center, center;
  margin: 20px;
}
</style>

### 使用box-shadow
```css
background: yellowgreen;
box-shadow: 0 0 0 10px #655, 0 0 0 15px deeppink;
```
注意点：box-shadow不影响布局和鼠标事件，可通过设置inset属性使得box-shaow投影在元素内部解决。
<div class="css-box2"></div>
<style>
.css-box2{
  width: 130px;
  height: 130px;
  box-sizing: border-box;
  background: yellowgreen;
  box-shadow: 0 0 0 5px deeppink inset, 0 0 0 15px #655 inset;
  margin: 20px;
}
</style>

### 双重边框可使用border + outline
```css
background: yellowgreen;
border: 10px solid #655;
outline: 5px solid deeppink;
```
outline能实现box-shaow实现不了的虚线等效果，并能通过outline-offset设置偏移值。
缺点是目前outline只能是矩形，如果存在border-radius，会存在空缺；可通过设置box-shadow来覆盖空缺。
<div class="css-box3"></div>
<style>
.css-box3{
  width: 100px;
  height: 100px;
  box-sizing: content-box;
  background: yellowgreen;
  border: 10px solid #655;
  outline: 5px solid deeppink;
  margin: 20px;
  margin-left: 25px;
}
</style>

## 灵活的背景定位
一个div,padding-rihgt为20px,padding-bottom为10px;要求背景在content-box内部（不覆盖padding）。
### 相对于右边和下方定位
```css
background-position: right 20px bottom 10px;
```
### 使用background-origin
```css
background-origin: content-box;
```
默认情况下，background-origin相对于padding-box为准，这样的话如果改变padding的值，需要同时修改background-position中的值，而改为相对于content-box后，就需要再修改。
### 使用calc
```css
background-position: calc(100% - 20px) calc(100% - 10px); 
```

## 边框内圆角
实现下面的边框内圆角使用两个div能轻松实现，如果要求只是用一个div呢？
<div class="css-box4">
  something is wrong
</div>
<style>
.css-box4 {
  width: 150px;
  height: 50px;
  background: tan;
  border-radius: .8em;
  outline: 0.4em solid black;
  box-shadow: 0 0 0 0.35em black;
  text-align: center;
  margin: 20px;
}
</style>
```css
background: tan;
border-radius: .8em;
outline: 0.4em solid black;
box-shadow: 0 0 0 0.35em black;
```
其中box-shadow的扩张值应大于等于($\sqrt{2}$-1)r, r为border-radius的值。

## 条纹背景
<style>
.css-box5 {
  width: 150px;
  height: 50px;
  background-image: linear-gradient(red 50%, yellow 80%);
  margin: 20px;
}
.css-box5.css-box5-vertical {
  background-image: linear-gradient(90deg, red 50%, yellow 80%);
}
.css-box5.css-box5-kink-simple {
  background-image: linear-gradient(60deg, red 20%, yellow 50%, green 90%);
  background-size: 30px 100%;
}
.css-box5.css-box5-kink {
  background-image: repeating-linear-gradient(60deg, #fb3, #fb3 15px, #58a 0,#58a 30px);
}
.css-box5.css-box5-similar {
  background: #58a;
  background-image:
    repeating-linear-gradient(30deg, transparent 0, transparent 15px, hsla(0, 0%, 100%, .1) 0,hsla(0, 0%, 100%, .1) 30px);
}
.css-box5.css-box5-zhishu {
  width: 300px;
  height: 100px;
  background: hsl(20, 40%, 90%);
  background-image:
    linear-gradient(90deg, #fb3 10px, transparent 0),
    linear-gradient(90deg, #ab4 20px, transparent 0),
    linear-gradient(90deg, #655 20px, transparent 0);
  background-size: 41px 100%, 61px 100%, 83px 100%;
}
</style>

### 水平条纹
默认为水平条纹：
<div class="css-box5"></div>
```css
background-image: linear-gradient(red 50%, yellow 80%);
```
解析一下，这里的意思是从0%-50%为red,从50%-80%渐变为yellow, 80%-100%为yellow。

### 垂直条纹
设置旋转角度90deg，为垂直条纹，顺时针旋转为正。
<div class="css-box5 css-box5-vertical"></div>
```css
background-image: linear-gradient(90deg, red 50%, yellow 80%);
```

### 斜向条纹
同样设置旋转角度即可。
<div class="css-box5 css-box5-kink-simple"></div>
```css
background-image: linear-gradient(60deg, red 20%, yellow 50%, green 90%);
background-size: 30px 100%;
```

### 更好的斜向条纹
可以看到，使用linear-gradient实现的斜向条纹在重复的时候不能得到应有的效果，使用repeating-linear-gradient来实现更好的斜向条纹。
<div class="css-box5 css-box5-kink"></div>
```css
background-image: repeating-linear-gradient(60deg, #fb3, #fb3 15px, #58a 0,#58a 30px);
```
对于0的解释：0会使用前面出现过最大的值替代，这里也就是15px, 使用这种方式可以简写;

### 灵活的同色系条纹
同色系条纹可以用透明层叠加的方式
<div class="css-box5 css-box5-similar"></div>
```css
background: #58a;
background-image:
    repeating-linear-gradient(30deg, transparent 0, transparent 15px, hsla(0, 0%, 100%, .1) 0,hsla(0, 0%, 100%, .1) 30px);
```

## 复杂的背景图案
<style>
  .css-box6 {
    width: 100px;
    height: 100px;
    background: tan;
    background-image:
      linear-gradient(white 1px, transparent 0),
      linear-gradient(90deg, white 1px, transparent 0);
    background-size: 30px 30px;
    margin: 20px;
  }
  .css-box7 {
    width: 100px;
    height: 100px;
    background: #655;
    background-image:
      radial-gradient(tan 30%, transparent 0),
      radial-gradient(tan 30%, transparent 0);
    background-size: 20px 20px;
    background-position: 0 0, 10px 10px;
    margin: 20px;
  }
  .css-box8 {
    width: 100px;
    height: 100px;
    background: white;
    background-image:
      linear-gradient(45deg, black 0, black 25%, transparent 0, transparent 75%, black 0, black 100%),
      linear-gradient(45deg, black 0, black 25%, transparent 0, transparent 75%, black 0, black 100%);
    background-size: 20px 20px;
    background-position: 0 0, 10px 10px;
    margin: 20px;
  }
</style>
### 网格
<div class="css-box6"></div>
```css
width: 100px;
height: 100px;
background: tan;
background-image:
  linear-gradient(white 1px, transparent 0),
  linear-gradient(90deg, white 1px, transparent 0);
background-size: 30px 30px;
```

### 波点
<div class="css-box7"></div>
```css
width: 100px;
height: 100px;
background: #655;
background-image:
  radial-gradient(tan 30%, transparent 0),
  radial-gradient(tan 30%, transparent 0);
background-size: 20px 20px;
background-position: 0 0, 10px 10px;
```

### 棋盘
<div class="css-box8"></div>
```css
width: 100px;
height: 100px;
background: white;
background-image:
  linear-gradient(45deg, black 0, black 25%, transparent 0, transparent 75%, black 0, black 100%),
  linear-gradient(45deg, black 0, black 25%, transparent 0, transparent 75%, black 0, black 100%);
background-size: 20px 20px;
background-position: 0 0, 10px 10px;
```
更多的：https://projects.verou.me/css3patterns/

## 伪随机背景
质数的使用:
<div class="css-box5 css-box5-zhishu"></div>
```css
background: hsl(20, 40%, 90%);
background-image:
  linear-gradient(90deg, #fb3 10px, transparent 0),
  linear-gradient(90deg, #ab4 20px, transparent 0),
  linear-gradient(90deg, #655 20px, transparent 0);
background-size: 41px 100%, 61px 100%, 83px 100%;
```

## 连续的图像边框
<style>
  @keyframes anti {
    to {
      background-position: 100%;
    }
  }
  .css-box9 {
    width: 100px;
    height: 50px;
    border: 5px solid transparent;
    background: 
      linear-gradient(white, white) padding-box,
      url('https://assets.codepen.io/t-1/user-default-avatar.jpg?fit=crop&format=auto&height=80&version=0&width=80') border-box 0 0 / cover;
    margin: 20px;
  }
  .css-box10 {
    width: 100px;
    height: 50px;
    border: 5px solid transparent;
    background: 
      linear-gradient(white, white) padding-box,
      repeating-linear-gradient(-45deg, red 0, red 12.5%, transparent 0, transparent 25%, blue 0, blue 37.5%, transparent 0, transparent 50%) 0 / 1em 1em;
    margin: 20px;
  }
  .css-box11 {
    width: 100px;
    height: 50px;
    border: 1px solid transparent;
    background: 
      linear-gradient(white, white) padding-box,
      repeating-linear-gradient(-45deg, black 0, black 25%, white 0, white 50%) 0 / .6em .6em;
    animation: anti 12s linear infinite;
    margin: 20px;
  }
  .css-box12 {
    border-top: .2em solid transparent;
    border-image: 100% 0 0 linear-gradient(90deg, black 0, black 5em, transparent 0);
    margin: 20px;
  }
</style>
### 图像边框随元素宽高和边框厚度改变
<div class="css-box9"></div>
```css
border: 5px solid transparent;
background: 
  linear-gradient(white, white) padding-box,
  url('https://assets.codepen.io/t-1/user-default-avatar.jpg?fit=crop&format=auto&height=80&version=0&width=80') border-box 0 0 / cover;
```
相当于用一个div实现了两个div(白色内容块+底部背景)的效果。

### 老式信封
<div class="css-box10"></div>
```css
width: 100px;
height: 50px;
border: 5px solid transparent;
background: 
  linear-gradient(white, white) padding-box,
  repeating-linear-gradient(-45deg, red 0, red 12.5%, transparent 0, transparent 25%, blue 0, blue 37.5%, transparent 0, transparent 50%) 0 / 1em 1em;
```
四个条纹需要8个色标，这里简化到50%只需要4个色标，如果写到100%，需要8个。

### 蚂蚁行军
<div class="css-box11"></div>
```css
width: 100px;
height: 50px;
border: 1px solid transparent;
background: 
  linear-gradient(white, white) padding-box,
  repeating-linear-gradient(-45deg, black 0, black 25%, white 0, white 50%) 0 / .6em .6em;
animation: anti 12s linear infinite;
```
```css
@keyframes anti {
  to {
    background-position: 100%;
  }
}
```

### 脚注
<div class="css-box12">
  <sup>1</sup>this is a sentence.
</div>
```css
border-top: .2em solid transparent;
border-image: 100% 0 0 linear-gradient(90deg, black 0, black 5em, transparent 0);
```