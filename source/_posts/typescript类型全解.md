---
title: typescript类型全解
date: 2022-04-17 16:57:55
tags:
 - typescript
categories:
 - typescript
cover: /svg/typescript.svg
bg_size: auto
---

## typescript类型

## 类型浅谈

### any
开启了strict: true, 隐式any会报错（函数中未定义类型的参数）；但是导入其他文件的时候不显示报错？（require('xxx.js')）

### unknown

### number

### string

### boolean

### 数组
数组
元组


### undefined、null、nerver、void
undefined: 变量未定义
null: 类型不存在
nerver：函数一直执行
void: 函数没有显示return

## 类型别名
```ts
type Age = number;
```