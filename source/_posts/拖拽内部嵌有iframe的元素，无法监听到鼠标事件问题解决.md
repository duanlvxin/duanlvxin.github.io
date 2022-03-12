---
title: 拖拽内部嵌有iframe的元素，无法监听到鼠标事件问题解决
date: 2022-03-12 20:26:50
tags:
  html
  事件
categories:
  html
cover: https://cn.bing.com/th?id=OHR.GreatCormorants_ZH-CN6811149253_1920x1080.jpg&rf=LaDigue_1920x1080.jpg
---

## 背景
单纯的拖拽实现起来很简单，主要原理就是在鼠标按下时，监听鼠标的mousemove事件，根据鼠标移动的距离调整元素的坐标值；在鼠标松开时，取消mousemove的监听事件。但如果被拖拽的元素内存在iframe,在iframe内的鼠标事件无法被监听到，就会存在一些问题。

## 解决方案
在该被拖拽元素外层套一层,当鼠标按下时，在该层监听事件，当鼠标松开时，隐藏该外层。可以通过调整z-index的值来实现。

## 实现
https://codepen.io/duanlvxin/pen/jOajwGO

比较：
(ps: codepen上演示有点问题，放到本地测试的)
不使用mask时，拖拽到最底部的时候，鼠标在iframe内松开，**还能**继续拖拽：
<video src="/video/before.mp4" width="100%" height="100%" controls="controls"></video>
使用mask时，拖拽到最底部的时候，鼠标在iframe内松开，**不能**继续拖拽：
<video src="/video/after.mp4" width="100%" height="100%" controls="controls"></video>

