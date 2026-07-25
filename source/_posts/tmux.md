---
title: tmux
date: 2026-07-24 20:24:25
tags:
 - 开发者工具 终端
categories:
 - 开发者工具
cover: /img/cover/tool.png
top_img: /img/default-top.jpg
---

## 详细文档
[tmux 官方文档](https://tmux.org/)
[tmux 入门教程](https://www.ruanyifeng.com/blog/2019/10/tmux.html)

## 简介
tmux 是一个终端复用工具，它可以在一个终端窗口中打开多个终端窗口，每个窗口都可以运行不同的命令。

## 安装
```bash
# Ubuntu 或 Debian
$ sudo apt-get install tmux

# CentOS 或 Fedora
$ sudo yum install tmux

# Mac
$ brew install tmux
```

## 使用
tmux 的使用方法简单，只需要在终端中输入 `tmux` 即可启动 tmux。
```bash
tmux

# 创建名为xxx的session
tmux new -s xxx
```

## 远程服务端（ssh）常用操作
```
1. 新建会话tmux new -s my_session。
2. 在 Tmux 窗口运行所需的程序。
3. 按下快捷键Ctrl+b d将会话分离。
4. 下次使用时，重新连接到会话tmux attach-session -t my_session。(tmux ls可看所有会话列表)
```

## 分屏
```
# 左右分屏，当前选中的屏会高亮, iterm里cmd+d也可以分屏
ctrl+b %

# 上下分屏
ctrl+b "

# 切换分屏
ctrl+b 方向键
```

## 窗口
```
# 新建窗口
ctrl+b c

# 切换窗口
ctrl+b [左下角的window数字] # 如ctrl+b 0，ctrl+b 1，...

# 关闭窗口
ctrl+b d

# 删除窗口
ctrl+d
exit
或者关闭窗口后用 tmux kill-session -t [window数字]

# 查看所有窗口列表
tmux ls

# 进入指定窗口
tmux a -t [window数字]  
```

## 配置文件
tmux 的配置文件是 `~/.tmux.conf`，你可以在其中自定义 tmux 的行为。
```
# 开启鼠标支持，可以点击分屏切换分屏，同时拖拽改变分屏大小
set -g mouse on
# 修改快捷键
set -g prefix C-s
```
