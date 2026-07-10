---
title: Mac 软件清单
date: 2026-07-10 11:32:00
tags:
- Mac
---

# Homebrew

[官网]: https://docs.brew.sh/Installation

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```



# git

[官网]: https://git-scm.com/install/mac

```
brew install git
```



# JDK

[sdkman]: https://sdkman.io

```
curl -s "https://get.sdkman.io" | bash 
```

```
# 安装对 Java 8 原生支持最好的 Zulu
sdk install java 8.0.492.zulu

# 安装 Java 17 或 21，用 Temurin
sdk install java 17.0.19-tem
sdk install java 21.0.11-tem
```



# Maven

```
# 查看所有可用的 Maven 版本
sdk list maven

# 安装你需要的版本，例如 3.8.6
sdk install maven 3.8.6

# 安装最新版本
sdk install maven
```



# Node

[fnm]: https://github.com/Schniz/fnm

```
brew install fnm
brew upgrade fnm
```

## Shell 設定（必做，否則指令不生效）

安裝完 fnm 本體只是第一步——還要讓 shell 認識它，自動版本切換才能運作。

### Zsh（`.zshrc`）

```zsh
eval "$(fnm env --use-on-cd --shell zsh)"
```

### Bash（`.bashrc`）

```bash
eval "$(fnm env --use-on-cd --shell bash)"
```
### 指定项目node版本
```bash
node --version > .node-version
```

> `--use-on-cd` 這個參數很關鍵——加了之後，進入有 `.nvmrc` 或 `.node-version` 的目錄，版本會自動切換，完全不用手動 `fnm use`。

## fnm常用指令一覽表

### 安裝 Node.js

| 指令                   | 說明                                     |
| ---------------------- | ---------------------------------------- |
| `fnm install <版本號>` | 安裝特定版本，例如 `fnm install 20.11.0` |
| `fnm install --lts`    | 安裝最新 LTS 版本                        |
| `fnm install latest`   | 安裝最新版本                             |

### 切換版本

| 指令                   | 說明             |
| ---------------------- | ---------------- |
| `fnm use <版本號>`     | 切換到指定版本   |
| `fnm use --lts`        | 切換到最新 LTS   |
| `fnm use latest`       | 切換到最新版本   |
| `fnm default <版本號>` | 設定全域預設版本 |

### 查詢版本

| 指令                                 | 說明                     |
| ------------------------------------ | ------------------------ |
| `fnm list` 或 `fnm ls`               | 列出本機已安裝的所有版本 |
| `fnm list-remote` 或 `fnm ls-remote` | 列出所有可安裝的遠端版本 |
| `fnm current`                        | 顯示目前使用的版本       |
| `node -v`                            | 確認 Node.js 版本        |
| `fnm --version`                      | 顯示 fnm 本身版本        |

### 移除版本

| 指令                     | 說明         |
| ------------------------ | ------------ |
| `fnm uninstall <版本號>` | 移除指定版本 |