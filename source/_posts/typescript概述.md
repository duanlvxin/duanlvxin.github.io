---
title: typescript概述
date: 2022-04-10 19:00:25
tags:
 - typescript
categories:
 - typescript
cover: /svg/typescript.svg
bg_size: auto
---

## 编译器

编译器做的三件事：
<pre>
1. 将代码转换为AST(抽象语法树)
2. 将AST编译成字节码
3. 运行时计算字节码
</pre>

TSC编译的过程：
<pre>
1. Typescript源码 -> Typescript AST
2. 类型检查器检查AST
3. Typescript AST -> javascript源码
</pre>
<pre>
4. javascript源码 -> javascript AST
5. javascript AST -> 字节码
6. 运行时计算字节码
</pre>

第1\~3步由TSC操作，第4\~6步由浏览器、NodeJs或其他Javascript引擎中的Javascript运行时操作。

编译型语言：**运行前**转换源码到字节码；解释型语言：**运行时**转换源码到字节码
Javascript编译器和运行时通常聚在一个称为引擎的程序中。程序员一般就是与引擎交互的。常见的引擎：V8、JSCore、Chakra。

## 类型系统 
> 类型系统：类型检查器为程序分配类型时使用的一系列规则

> 类型系统有两种：一种是显式的(java、c、c++等)，一种是自动推导的(javascript、python等)

Typescript是两者的结合，既可以显示注解类型，也可以自动推导。

## Typescript和Javascript类型比较
| 类型系统特性 | Typescript | Javascript |
|  :----  | :----  | :----  |
| 类型是如何绑定的？| 静态 | 动态 |
| 是否自动转换类型？| 否（多数时候） | 是 |
| 何时检查类型？| 编译时 | 运行时 |
| 何时报告错误？| 编译时（多数时候） | 运行时（多数时候） |

## 代码编辑器设置

如何从零创建一个typescript简单项目：
1. 下载依赖
```bash
npm install typescript tslint @types/node
```
2. 配置ts: tsconfig.js
```bash
npx typescript --init
```
详细的配置略，配置入口和出口：
```json
{
  "compilerOptions":{
    ...,
    "outDir": "dist"
  },
  "include": [
    "src"
  ]
}
```

3. 配置tslint: tslint.js
```bash
npx tslint --init
```

4. 新建src/index.ts写入一些内容并编译执行
```bash
touch src/index.ts
npx tsc
```
执行完成后可以看到生成了dist/index.js文件，运行该文件
```bash
node dist/index.js
```

利用工具创建typescript项目：
1. ts-node: 只需一个命令便能编译和运行typescript代码
2. typescript-node-starter(https://github.com/Microsoft/TypeScript-Node-Starter): typescript-node脚手架

