---
title: hexo+butterfly主题+自动部署构建博客
date: 2022-03-12 22:13:45
tags:
  - hexo
categories:
  - hexo
---

# 使用hexo搭建博客

## 开始

```bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

## 命令配置

```bash
server: "hexo server --log" // 添加--log打印日志
```



## 插件

### 热更新

```bash
npm install hexo-browsersync --save
```

​		注意：更改_config.yml等配置文件需要重新运行；


# butterfly主题配置
## 主题下载使用

butterfly [https://butterfly.js.org/posts/21cfbf15/#%E5%AE%89%E8%A3%9D]

可以使用npm包，也可以克隆代码。这边选择克隆代码，好处是可以更自由地编辑，不过clone 下来目录下存在.git文件夹，需要进行删除：可参考这篇文章 https://blog.csdn.net/xiebaochun/article/details/114143346
### 第一步

```bash
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
```

会在themes文件夹下生成butterfly目录

### 第二步

修改_config.yaml里的theme

```yaml
theme: butterfly
```

### 第三步

安装pug和stylus渲染器

```bash
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

## 主题配置优化


# 部署

## 一键部署
```bash
npm install hexo-deployer-git --save
```

​		编辑_config.yaml

```yaml
url: https://duanlvxin.github.io

deploy:
  type: git
  repo: https://github.com/duanlvxin/duanlvxin.github.io.git
  branch: master
```

​		执行部署命令

```bash
hexo clean && hexo deploy
```

​	查看\<username\>.github.io

## 使用github actions
在.github的workflows文件下新建deploy.yml(文件名可以变，文件夹必须是workflows)

源码在dev分支上，打包好的文件在master上；
HEXO_DEPLOY_KEY是github上配置的

```yml
name: Hexo Deploy

on:
  push:
    branches:
      - dev
    
jobs:
  build:
    runs-on: ubuntu-18.04
    if: github.event.repository.owner.id == github.event.sender.id

    steps:
      - name: Checkout source
        uses: actions/checkout@v2
        with:
          ref: dev

      - name: Setup Node.js
        uses: actions/setup-node@v1
        with:
          node-version: '16'

      - name: Setup Hexo
        run: |
          npm install hexo-cli -g
          npm install
          
      - name: Build
        run: |
          hexo clean
          hexo generate

      - name: Deploy
        uses: JamesIves/github-pages-deploy-action@v4.2.5
        with:
          branch: master
          folder: public
          ssh-key: ${{ secrets.HEXO_DEPLOY_KEY }}

```