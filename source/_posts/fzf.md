---
title: fzf
date: 2026-07-24 20:24:25
tags:
 - 开发者工具 文件查找
categories:
 - 开发者工具
---

## 简介
fzf 是一个模糊查找工具，它可以在文件系统中快速查找文件或目录。
fzf 的使用方法简单，只需要在终端中输入 `fzf` 即可启动 fzf。
[fzf 官方文档](https://junegunn.github.io/fzf/)

## 安装
```bash
# 如果有报错，可能是国内镜像还没同步，先加上下面这句
# export HOMEBREW_BOTTLE_DOMAIN=''
# Mac
brew install fzf

# 高亮，建议安装
brew install bat

## 绑定zsh(~/.zshrc)
source <(fzf --zsh)
```

## 使用
### 搜索文件
```bash
# 递归列出所有文件
fzf

# 查找文件并打开-方式一
vim $(fzf)
# 查找文件并打开-方式二
fzf | xargs bat
fzf | xargs vim
```
### 预览文件
```bash
fzf --preview "fzf-preview.sh {}"
```
![alt text](/img/fzf/preview.png)


### 历史命令ctrl+r
```bash
# 直接输入ctrl+r 即可现实历史命令
ctrl + r
```
![alt text](/img/fzf/ctrl+r.png)

### 和其他命令配置
#### ctrl+t
```bash
# bat 命令/其他命令 ，按ctrl+t会显示当前目录下的所有文件和目录
# 可以单选，也可以用tab键多选
# 也可以用后面的bat ** + tab键
bat ctrl+t
```
![alt text](/img/fzf/ctrl+t.png)

#### alt+c
```bash
# 进入选择的目录，不好用，可以直接cd ** + 按下tab键
alt + c
```

### zsh+fzf
```bash
# 列出当前目录下的所有文件和目录，和ctrl+t功能相同
bat ** + 按下tab键

# 列出所有登录过的主机名
ssh ** + 按下tab键

# 列出所有进程
kill ** + 按下tab键

# 列出当前环境的所有变量
unset ** + 按下tab键

# 列出所有别名
unalias ** + 按下tab键
```

## 配置
### 自定义别名

配置fzfp别名，用来预览文件

```bash
# 自定义别名 fzfp
alias fzfp="fzf --style full --preview 'fzf-preview.sh {}'"
```

### 自定义**用法

支持cd ** + 按下tab键, 可以预览文件结构(类似tree)

```bash
_fzf_comprun() {
  local command=$1
  shift

  case "command" in
    cd) fzf --preview "tree {}" "$@" ;;
    *) fzf "$@" ;;
  esac
}
```

![alt text](/img/fzf/cd.png)
