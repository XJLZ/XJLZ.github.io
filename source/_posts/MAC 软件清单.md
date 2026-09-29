---
title: Mac开发 软件清单
date: 2026-07-10 11:32:00
tags:
- Mac
---

# Homebrew

[官网]: https://docs.brew.sh/Installation

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 中科大源
/bin/bash -c "$(curl -fsSL https://mirrors.ustc.edu.cn/misc/brew-install.sh)"
```

**若下载比较慢，手动安装 Command Line Tools 26.6**

https://developer.apple.com/download/all/

## 配置镜像源

```bash
nano ~/.zshrc
# Homebrew 镜像 中科大源
export HOMEBREW_API_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/api
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
export HOMEBREW_BREW_GIT_REMOTE=https://mirrors.ustc.edu.cn/brew.git
# 清华
#export HOMEBREW_API_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/api
#export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles
#export HOMEBREW_BREW_GIT_REMOTE=https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

#更新
source ~/.zshrc
brew update
brew config
```

# Git

[官网](https://git-scm.com/install/mac)

```
brew install git
```

# iTerm + oh-my-zsh

[iTerm官网](https://iterm2.com/index.html)

## 安装 iTerm2

```
brew install --cask iterm2
```

## 安装oh-my-zsh

**先备份配置文件 ~/.zshrc**

```bash
# 备份
cp ~/.zshrc ~/.zshrc.bak
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 安装常用插件

```bash
# 命令补充
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
# 高亮
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## 修改配置

[ohmyzsh主题](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)

```bash
vim ~/.zshrc

# 找到配置项进行修改

# 修改主题 
ZSH_THEME="ys"
# 修改插件
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)

# 保存后加载
source ~/.zshrc
```

## 修改vim设置

```bash
vim ~/.vimrc

" 开启语法高亮
syntax enable
" 开启文件类型检测
filetype on
" 开启插件和缩进
filetype plugin indent on
```

# JDK

[sdkman](https://sdkman.io)

```bash
curl -s "https://get.sdkman.io" | zsh 
```

**修改配置**

```bash
vim ~/.zshrc

# 新增配置

# sdkman加载
export SDKMAN_DIR="$HOME/.sdkman"
___MY_VMOPTIONS_SHELL_FILE="${HOME}/.jetbrains.vmoptions.sh"; if [ -f "${___MY_VMOPTIONS_SHELL_FILE}" ]; then . "${___MY_VMOPTIONS_SHELL_FILE}"; fi
[[ -s "$SDKMAN_DIR/bin/sdkman-init.sh" ]] && source "$SDKMAN_DIR/bin/sdkman-init.sh"

# 保存后加载
source ~/.zshrc
```

```bash
# 安装对 Java 8 原生支持最好的 Zulu
sdk install java 8.0.492-zulu

# 安装 Java 17 或 21，用 Temurin
sdk install java 17.0.19-tem
sdk install java 21.0.11-tem

# 卸载
sdk uninstall java 11.0.22-tem

# 切换java
sdk use java 21.0.11-tem
```

```
# 路径
/Users/xiao/.sdkman/candidates/java
```

## 使用

 进入项目：

```bash
cd java-project
```

设置：

```bash
sdk env init
```

会生成：

```bash
.sdkmanrc
sdk env
```

内容：

```bash
# Enable auto-env through the sdkman_auto_env config
# Add key=value pairs of SDKs to use below
java=21.0.11-tem
```

# Maven

[sdkman](https://sdkman.io)

```bash
# 查看所有可用的 Maven 版本
sdk list maven

# 安装你需要的版本，例如 3.8.6
sdk install maven 3.8.6

# 安装最新版本
sdk install maven
```

```
# 路径
/Users/xiao/.sdkman/candidates/maven
```

# SDKMAN

[sdkman](https://sdkman.io)

```sh
# 查看已安装版本
sdk list java
sdk list maven

# 仅当前 iTerm 窗口生效
sdk use java 17.0.19-tem
sdk use maven 3.6.3

# 设置默认版本，新开终端也生效
sdk default java 17.0.19-tem
sdk default maven 3.6.3

# 查看当前版本
sdk current
```

修改.zshrc

```sh
# sdkman
#THIS MUST BE AT THE END OF THE FILE FOR SDKMAN TO WORK!!!
export SDKMAN_DIR="$HOME/.sdkman"
___MY_VMOPTIONS_SHELL_FILE="${HOME}/.jetbrains.vmoptions.sh"; if [ -f "${___MY_VMOPTIONS_SHELL_FILE}" ]; then . "${___MY_VMOPTIONS_SHELL_FILE}"; fi
[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"

```

# Node

[fnm](https://github.com/Schniz/fnm)

```bash
brew install fnm
brew upgrade fnm
```

## Shell 設定（必做，否則指令不生效）

安裝完 fnm 本體只是第一步——還要讓 shell 認識它，自動版本切換才能運作。

```bash
nano ~/.zshrc
eval "$(fnm env --use-on-cd)"
# fnm node下载镜像
export FNM_NODE_DIST_MIRROR=https://npmmirror.com/mirrors/node
# npm镜像源
npm_config_registry=https://registry.npmmirror.com

source ~/.zshrc
```

### 指定项目node版本

```bash
node --version > .node-version
```

> `--use-on-cd` 這個參數很關鍵——加了之後，進入有 `.nvmrc` 或 `.node-version` 的目錄，版本會自動切換，完全不用手動 `fnm use`。

## fnm常用指令一覽表

### 安裝 Node.js

| 指令                   | 說明                         |
| -------------------- | -------------------------- |
| `fnm install <版本號>`, | 安裝特定版本，例如 `fnm install 24` |
| `fnm install --lts`  | 安裝最新 LTS 版本                |
| `fnm install latest` | 安裝最新版本                     |

### 切換版本

| 指令                  | 說明                   |
| ------------------- | -------------------- |
| `fnm use <版本號>`     | 切換到指定版本,`fnm use 14` |
| `fnm use --lts`     | 切換到最新 LTS            |
| `fnm use latest`    | 切換到最新版本              |
| `fnm default <版本號>` | 設定全域預設版本             |

### 查詢版本

| 指令                                  | 說明            |
| ----------------------------------- | ------------- |
| `fnm list` 或 `fnm ls`               | 列出本機已安裝的所有版本  |
| `fnm list-remote` 或 `fnm ls-remote` | 列出所有可安裝的遠端版本  |
| `fnm current`                       | 顯示目前使用的版本     |
| `node -v`                           | 確認 Node.js 版本 |
| `fnm --version`                     | 顯示 fnm 本身版本   |

### 移除版本

| 指令                    | 說明     |
| --------------------- | ------ |
| `fnm uninstall <版本號>` | 移除指定版本 |

## PM2

```bash
# 安装
npm install -g pm2
# 日志切割
pm2 install pm2-logrotate
pm2 set pm2-logrotate:max_size 10M
pm2 set pm2-logrotate:retain 30
pm2 set pm2-logrotate:compress true
pm2 set pm2-logrotate:dateFormat YYYY-MM-DD
```

## pnpm

```bash
npm install -g pnpm
```

# Docker

[官网](https://www.docker.com/get-started/)

https://github.com/datawhalechina/docker-notes/blob/main/docs/ch2/ch2_2.md

https://phoenixnap.com/kb/install-docker-macos

```bash
brew install --cask docker

# 兼容X86
softwareupdate --install-rosetta
```

# Python

```bash
# 安装 pyenv
brew install pyenv

# 修改配置
vim ~/.zshrc

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

source ~/.zshrc


# 验证
pyenv --version

# 查看可安装版本
pyenv install --list
# 安装
pyenv install 3.12.13
# 检查
pyenv versions
# 设置默认版本
pyenv global 3.12.13
```

## pip 镜像配置

创建：

```bash
mkdir -p ~/.config/pip

vim ~/.config/pip/pip.conf
```

内容：

```bash
[global]
index-url=https://pypi.tuna.tsinghua.edu.cn/simple

[install]
trusted-host=pypi.tuna.tsinghua.edu.cn
```

测试：

```bash
pip install requests
```

## 使用

进入项目：

```
cd my-project
```

设置：

```
pyenv local 3.12.13
```

会生成：

```
.python-version
```

内容：

```
3.12.13
```

以后进入目录自动切换。

例如：

```
project-a
 ├── .python-version  -> 3.12.13

project-b
 ├── .python-version  -> 3.12.4
```

# 常用软件

- Raycast

- Background Music

- Stats

- AppCleaner

- IINA

- Termius

- marktext、Typora

- Keka

- Snipaste

- Piexa

- Parabolic、Stacher7、Open Video Downloader

- Tiny RDM

- SwichHosts

- Motrix

- PicGo

## 
