---
title: html属性顺序校验和排序
date: 2022-03-06 17:01:41
categories:
  - js
tags:
  - html
  - js
  - 算法
cover: https://i0.hippopx.com/photos/282/563/731/code-website-html-web-preview.jpg
---

## 背景
在使用vue编写.vue文件时，为了代码风格的统一，需要规范html标签中属性的顺序。
不同的团队对于顺序的定义不同，以我们的团队为例，约定好的顺序需要为:
```
v-xx=xx > xx=xx > :xx=xx  > @xx=xx
```
那么如果我们需要校验属性顺序是否符合这个规则，如果不符合进行排序使其符合要怎么做呢？

## 思路
对于判断是否符合规则：
1. 定义一个map对象存储属性与优先级的关系，codeMap
2. 将属性数组中的属性根据这个codeMap映射出优先级数组。
3. 问题就转换为判断这个数组是否是升序（或降序，根据codeMap中对于优先级的定义而定）

对于排序：
1. 可以用js的sort函数，只需要实现传入的参数：compare函数
2. compare函数中的大小比较即是根据属性的优先级大小。

## 实现
对于判断是否符合规则：
1. 定义codeMap,这里数字越小，优先级越高
```js
const codeMap = new Map([
  [new RegExp(/^v-.+=.*$/), 1],
  [new RegExp(/^[^@:]+=.*$/), 2],
  [new RegExp(/^:.+=.*$/), 3],
  [new RegExp(/^@.+=.*$/), 4]
])
```
2. 生成优先级数组
```js
function getWeight(str) {
  for(let [key, value] of codeMap) {
    if(key.test(str)) {
      return value;
    }
  }
}

function getWeightArr (arr) {
  const res = [];
  for(let item of arr) {
    res.push(getWeight(item));
  }
  return res;
}
```
3. 判断数组是否升序
```js
function isAscending (arr) {
  for(let i=0,len=arr.length-1;i<len;i++){
    if(arr[i]>arr[i+1]){
      return false;
    }
  }
  return true;
}
```

对于排序：
1. 只需实现compare函数
```js
function compare(a, b) {
  const weightA = getWeight(a);
  const weightB = getWeight(b);
  return weightA - weightB;
}
```

## 测试
以下面这个数组为例:
```js
const arr = [":visible2='visible2'", "style='color: red'", ":visible='visible'", "class = 'test", "@click='handleClick'"];
```

```js
const weightArr = getWeightArr(arr);
const sortedArr = arr.sort(compare);
console.log(sortedArr);
const sortedWeightArr = getWeightArr(sortedArr);
console.log(isAscending(weightArr));
console.log(isAscending(sortedWeightArr));
```

打印结果：
![alt 结果](../img/html属性-结果.png "结果")