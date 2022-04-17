---
title: css字体排印
date: 2022-04-10 14:47:06
tags:
  - css
  - css揭秘
categories:
  - css
cover: /img/css-secret.jpg
---
<style>
  .example {
    margin: 20px;
  }
  .box-inline {
    display: inline-block;
    border: 2px solid pink;
    padding: 10px;
  }
</style>
## 连字符断行
```css
hyphens: auto;
```

未进行处理 vs 手动使用软连接&shy; vs hyphens:auto的效果比较：
<div class="example box-inline" style="width: 140px;">
  "The only way to get rid of a temptation is to yield to it."
</div>

<div class="example box-inline" style="width: 140px;">
  "The only way to get rid of a temp&shy;tation is to yield to it."
</div>

<div lang="en" class="example box-inline" style="width: 140px; hyphens: auto;">
  "The only way to get rid of a temptation is to yield to it."
</div>

*注意*: 使用hyphens时需要指定lang, 如lang="en"


## 插入换行
<style>
  dl {
    border: 1px solid black;
    padding: 10px;
  }
  dd {
    margin-inline-start: 0 !important;
    font-weight: bold;
  }
  dd, dt {
    display: inline;
  }
  dd + dt::before {
    content: "\A";
    white-space: pre;
  }
  dd + dd::before {
    content: ', ';
    font-weight: normal;
  }
</style>
<dl>
  <dt>name:</dt>
  <dd>duan</dd>

  <dt>email:</dt>
  <dd>2316636696@qq.com</dd>

  <dd>dlx12345678@163.com</dd>

  <dt>address:</dt>
  <dd>someaddress</dd>
</dl>

```html
<dl>
  <dt>name:</dt>
  <dd>duan</dd>

  <dt>email:</dt>
  <dd>2316636696@qq.com</dd>

  <dd>dlx12345678@163.com</dd>

  <dt>address:</dt>
  <dd>someaddress</dd>
</dl>
```

```css
dl {
  border: 1px solid black;
  padding: 10px;
}
dd {
  margin-inline-start: 0 !important;
  font-weight: bold;
}
dd, dt {
  display: inline;
}
dd + dt::before {
  content: "\A";
  white-space: pre;
}
dd + dd::before {
  content: ', ';
  font-weight: normal;
}
```

## 文本行的斑马效果
<style>
  pre.banma {
    background: beige !important;
    background-image: repeating-linear-gradient(rgba(0,0,0,.2), rgba(0,0,0,.2) 1.6em, transparent 0, transparent 3.2em) !important;
    background-clip: content-box !important;
    background-origin: content-box !important;
    color: #58a !important;
  }
</style>

<pre style="tab-size: 2;" class="banma">
while(true) {
&#0009;var d = new Date();
&#0009;&#0009;if(d.getDate()===1&&d.getMonth()===3){
&#0009;&#0009;alert("april fool's day");
&#0009;}
}
</pre>

原理：利用css渐变生成背景图片:
```css
background: beige;
color: #58a;
background-image: repeating-linear-gradient(rgba(0,0,0,.2), rgba(0,0,0,.2) 1.6em, transparent 0, transparent 3.2em);
background-clip: content-box;
background-origin: content-box;
```

## 调整tab的宽度
```css
tab-size: 3;
```
<pre style="-moz-tab-size: 3; -webkit-tab-size: 3; tab-size:3;">
while(true) {
&#0009;var d = new Date();
&#0009;&#0009;if(d.getDate()===1&&d.getMonth()===3){
&#0009;&#0009;alert("april fool's day");
&#0009;}
}</pre>

*注意*: tab制表符为`&#0009`;

## 连字
> 在css字体（第三版）[https://w3.org/TR/css3-fonts] 中，原有的font-variant被升级成了一个简写属性，由很多新的展开式属性组合而成。其中之一叫做font-variant-ligatures,专门用来控制连字效果的开启和关闭。

启用所有可能的连字，需要同时指定这三个标识符：
```css
font-variant-ligarures: common-ligatures // 通用连字
                        discretionary-ligatures // 酌情连字
                        historical-ligatures //历史连字，老书中的连字
```

需要关闭某些连字时：
```css
font-variant-ligarures: common-ligatures
                        no-discretionary-ligatures
                        no-historical-ligatures
```

font-variant-ligatures还可以为none, 他会把所有的连字效果关掉，最好不要用；如果要把font-variant-ligatures属性复位为初始值，使用normal而不是none。

## 华丽的&符号
<style>
  @font-face {
    font-family: Ampersand;
    src: local('Baskerville-Italic'),
         local('Goudy Old Style-Italic'),
         local('Garamond-Italic'),
         local('Palatino-Italic');
    unicode-range: U+26;
  }
  .petty-font {
    font-family: Ampersand;
  }
</style>
<span class="petty-font">HTML & CSS</span>
```css
@font-face {
  font-family: Ampersand;
  src: local('Baskerville-Italic'),
        local('Goudy Old Style-Italic'),
        local('Garamond-Italic'),
        local('Palatino-Italic');
  unicode-range: U+26; // &符号的unicode编号为26
}
.petty-font {
  font-family: Ampersand;
}
```

## 自定义下划线
<style>
  .text-underline span{
    box-shadow: 0 1px gray;
  }
  .text-underline2 span {
    background: linear-gradient(gray,gray) no-repeat;
    background-size: 100% 1px;
    background-position: 0 1.15em;
    text-shadow: .05em 0 white, -.05em 0 white;
  }
  .text-underline3 span {
    background: linear-gradient(90deg, gray 66%, white 0) repeat-x;
    background-size: .8em 2px;
    background-position: 0 1.15em;
  }
  .text-underline4 span {
    background: 
    radial-gradient(white 40%, transparent 0), radial-gradient(white 40%, transparent 0), radial-gradient(#58a 40%, transparent 0), radial-gradient(#58a 40%, transparent 0);
    background-size: .5em 1em;
    background-repeat: repeat-x;
    background-position: 0 1em, .25em 1em, 0 .9em, .25em .9em;
    text-shadow: .05em 0 white, -.05em 0 white;
  }
  .text-underline5 span {
    background: 
    linear-gradient(-45deg, transparent 40%, red 0, red 60%, transparent 0) 0 1.2em,
	  linear-gradient(45deg, transparent 40%, red 0, red 60%, transparent 0) .1em 1.2em;
    background-repeat: repeat-x;
    background-size: .2em .1em;
    text-shadow: .05em 0 white, -.05em 0 white;
  }
</style>
### 实线
<div class="example box-inline text-underline" style="width: 140px; hyphens: auto;">
  "The only way to <span>get rid of a temp&shy;tation is</span> to <span>yield to it."</span>
</div>
<div class="example box-inline text-underline2" style="width: 140px; hyphens: auto;">
  "The only way to <span>get rid of a temp&shy;tation is</span> to <span>yield to it."</span>
</div>

左图使用的是box-shadow:
```css
box-shadow: 0 1px gray;
```

右图使用的是background，利用渐变:
```css
background: linear-gradient(gray,gray) no-repeat;
background-size: 100% 1px;
background-position: 0 1.15em;
text-shadow: .05em 0 white, -.05em 0 white; //使得下划线在文字部分断开
```

### 虚线
<div class="example box-inline text-underline3" style="width: 140px; hyphens: auto;">
  "The only way to <span>get rid of a temp&shy;tation is</span> to <span>yield to it."</span>
</div>
```css
background: linear-gradient(90deg, gray 66%, white 0) repeat-x;
background-size: .8em 2px;
background-position: 0 1.15em;
```

### 波浪线

<div class="example box-inline text-underline4" style="width: 140px; hyphens: auto;">
  "<span>The only way to </span>get rid of a temp&shy;tation is to yield to it."
</div>

<div class="example box-inline text-underline5" style="width: 140px; hyphens: auto;">
  "<span>The only way to </span>get rid of a temp&shy;tation is to yield to it."
</div>

```css
background: 
radial-gradient(white 40%, transparent 0), radial-gradient(white 40%, transparent 0), radial-gradient(#58a 40%, transparent 0), radial-gradient(#58a 40%, transparent 0);
background-size: .5em 1em;
background-repeat: repeat-x;
background-position: 0 .95em, .25em .95em, 0 .9em, .25em .9em;
text-shadow: .05em 0 white, -.05em 0 white;
```
*注意*: radial-gradient的百分比是相对于矩形的斜边的。

```css
background: 
linear-gradient(-45deg, transparent 40%, red 0, red 60%, transparent 0) 0 1.2em,
linear-gradient(45deg, transparent 40%, red 0, red 60%, transparent 0) .1em 1.2em;
background-repeat: repeat-x;
background-size: .2em .1em;
text-shadow: .05em 0 white, -.05em 0 white;
```

## 现实中的文字效果
<style>
  .font-box {
    width: 150px;
    padding: 10px;
  }
  .font-circle-box {
    background: deeppink;
    color: white;
    font-size: 24px;
    text-align: center;
  }
  .font-box1 {
    background: hsl(210, 13%, 60%);
    color: hsl(210, 13%, 30%);
    text-shadow: 0 1px 1px hsla(0,0%,100%,.8);
  }
  .font-box2 {
    color: hsl(210, 13%, 60%);
    background: hsl(210, 13%, 30%);
    text-shadow: 0 -1px 1px black;
  }
  .font-circle-box1 {
    text-shadow: 1px 1px black, -1px -1px black,
    1px -1px black, -1px 1px black;
  }
  .font-circle-box2 {
    text-shadow: 0 0 2px black,0 0 2px black,
    0 0 2px black,0 0 2px black,
    0 0 2px black,0 0 2px black,
    0 0 2px black,0 0 2px black;
  }
  .font-circle-box3 text {
    fill: currentColor;
  }
  .font-circle-box3 svg {
    overflow: visible;
  }
  .font-circle-box3 use {
    stroke: green;
    stroke-width: 6;
    stroke-linejoin: round;
  }
</style>
### 凸版印刷效果
<div class="example font-box font-box1" style="display: inline-block;">The only way to get rid of a temptation is to yield to it.</div>
<div class="example font-box font-box2" style="display: inline-block;">The only way to get rid of a temptation is to yield to it.</div>

浅色背景，深色文字 -> 使用浅色投影实现凸起效果
```css
background: hsl(210, 13%, 60%);
color: hsl(210, 13%, 30%);
text-shadow: 0 1px 1px hsla(0,0%,100%,.8);
```

深色背景，浅色文字 -> 使用深色投影实现凸起效果
```css
color: hsl(210, 13%, 60%);
background: hsl(210, 13%, 30%);
text-shadow: 0 -1px 1px black;
```

### 空心字效果
<div class="example font-box font-circle-box font-circle-box1">css</div>
```css
text-shadow: 1px 1px black, -1px -1px black,
    1px -1px black, -1px 1px black;
```

<div class="example font-box font-circle-box font-circle-box2">css</div>
```css
text-shadow: 0 0 2px black,0 0 2px black,
    0 0 2px black,0 0 2px black,
    0 0 2px black,0 0 2px black,
    0 0 2px black,0 0 2px black;
```

<div class="example font-box font-circle-box font-circle-box3">
  <svg width="2em" height="1.2em">
    <use xlink:href="#css" />
    <text id="css" y="1em">css</text>
  </svg>
</div>

```html
<div class="box">
  <svg width="2em" height="1.2em">
    <use xlink:href="#css" />
    <text id="css" y="1em">css</text>
  </svg>
</div>
```

```css
.box {
  background: deeppink;
  color: white;
  font-size: 24px;
  text-align: center;
}
.box .text {
  fill: currentColor;
}
.box .svg {
  overflow: visible;
}
.box .use {
  stroke: green;
  stroke-width: 6;
  stroke-linejoin: round;
}
```

### 文字外发光效果
<style>
  .font-light-box, .font-light-box2 {
    background: #203;
    color: #ffc;
    text-align: center;
    font-size: 24px;
    transition: 1s;
    cursor: pointer;
  }
  .font-light-box:hover {
    text-shadow: 0 0 .1em, 0 0 .3em;
  }
  .font-light-box2:hover {
    text-shadow: 0 0 .1em white, 0 0 .3em white;
    color: transparent;
  }
</style>
鼠标hover之后展示发光效果：
<div class="example font-box font-light-box">blow</div>

```css
.font-light-box {
  background: #203;
  color: #ffc;
  text-align: center;
  font-size: 24px;
  transition: 1s;
  cursor: pointer;
}
```

```css
.font-light-box:hover {
  text-shadow: 0 0 .1em, 0 0 .3em;
}
```

鼠标hover之后展示模糊效果：
<div class="example font-box font-light-box2">blow</div>

```css
.font-light-box {
  background: #203;
  color: #ffc;
  text-align: center;
  font-size: 24px;
  transition: 1s;
  cursor: pointer;
}
```

隐藏文字，使用投影来模拟模糊效果：（如果浏览器不支持text-shadow,hover之后会什么都看不见）
```css
.font-light-box:hover {
  text-shadow: 0 0 .1em white, 0 0 .3em white;
  color: transparent;
}
```

使用滤镜：（如果浏览器不支持filter, 会平稳退化，hover之后没有模糊效果，但文字仍然保留）
```css
.font-light-box:hover {
  filter: blur(.1em);
}
```


### 文字凸起效果
<style>
  .font-3d-box {
    background: #58a;
    color: white;
    text-align: center;
    font-size: 60px;
  }
  .font-3d-box1 {
    text-shadow: 0 1px hsl(0,0%,85%),
    0 2px hsl(0,0%,80%),
    0 3px hsl(0,0%,75%),
    0 4px hsl(0,0%,70%),
    0 5px hsl(0,0%,65%),
    0 5px 10px black;
  }
  .font-3d-box2 {
    width: 300px;
    color: white;
    background: hsl(0, 50%, 45%);
    text-shadow: 1px 1px black,
    2px 2px black,
    3px 3px black,
    4px 4px black,
    5px 5px black,
    6px 6px black,
    7px 7px black,
    8px 8px black,
    9px 9px black;
  }
</style>
<div class="example font-box font-3d-box font-3d-box1">css</div>
```css
text-shadow: 0 1px hsl(0,0%,85%),
  0 2px hsl(0,0%,80%),
  0 3px hsl(0,0%,75%),
  0 4px hsl(0,0%,70%),
  0 5px hsl(0,0%,65%),
  0 5px 10px black;
```

<div class="example font-box font-3d-box font-3d-box2">RETRO</div>
```css
width: 300px;
  color: white;
  background: hsl(0, 50%, 45%);
  text-shadow: 1px 1px black,
    2px 2px black,
    3px 3px black,
    4px 4px black,
    5px 5px black,
    6px 6px black,
    7px 7px black,
    8px 8px black,
    9px 9px black;
```

## 环形文字
<style>
  .circular {
    width: 10em;
    height: 10em;
    padding: 1em;
    position: relative;
  }
  .circular path {
    fill: none;
  }
  .circular svg {
    overflow: visible;
  }
  .circular img {
    position: absolute;
    top: 1.5em;
    left: 1.5em;
    border-radius: 50%;
    width: calc(100% - 3em);
    height: calc(100% - 3em);
  }
</style>
<div class="example circular">
  <svg viewBox="0 0 100 100">
  <path d="M 0,50 a 50,50 0 1,1 0,1 z" id="circle" />
    <text>
      <textpath xlink:href="#circle">
      circle reasoning works because something.circle works.
      </textpath>
    </text>
  </svg>
  <img src="/img/avatar.jpg">
</div>

```html
<div class="circular">
  <svg viewBox="0 0 100 100">
  <path d="M 0,50 a 50,50 0 1,1 0,1 z" id="circle" />
    <text>
      <textpath xlink:href="#circle">
      circle reasoning works because something.circle works.
      </textpath>
    </text>
  </svg>
</div>
```

```css
.circular {
  width: 10em;
  height: 10em;
}
.circular path {
  fill: none;
}
.circular svg {
  overflow: visible;
}
```


