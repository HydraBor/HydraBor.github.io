---
title: 在 Linux 上使用官方二进制包安装 Node.js
date: 2024-06-17 21:16:40
updated: 2026-09-16 23:28:16
author: Algor Turling
description: 使用 Node.js 官方 Linux 二进制压缩包安装 Node.js 和 npm，说明架构选择、SHA-256 校验、Bash 环境变量与安装验证。
categories:
  - 环境配置
tags:
  - Node.js
  - Linux
---

本文记录使用官方预编译二进制压缩包安装 Node.js 的方法，适用于希望手动管理安装目录的 Linux 用户。压缩包已包含 Node.js 和 npm，无需编译源码。

<!-- more -->

本文最初发布于 2024 年 6 月，使用 Node.js 20.14.0 举例。2026 年 9 月修订时，Node.js 20 已结束支持，下面改用 **Node.js 24.21.0 LTS**。后续安装请先查看 [Node.js 下载页](https://nodejs.org/en/download)和[版本支持状态](https://nodejs.org/en/about/previous-releases)，选择仍受支持的 LTS 版本。

## 1. 确认系统和架构

以下命令以 **Bash** 为例。系统需要具备 `curl`、`tar`、`xz`、`sha256sum`，安装到 `/usr/local/lib/nodejs` 时还需要 `sudo` 权限。

先查看 CPU 架构：

```bash
uname -m
```

| 命令输出 | 下文的 `node_arch` |
| --- | --- |
| `x86_64` | `x64` |
| `aarch64` 或 `arm64` | `arm64` |

这里使用的是面向 glibc Linux 的官方二进制包。Node.js 24 的相关平台要求包括 Linux 内核至少 4.18、glibc 至少 2.28，以及相应的 libstdc++ 版本；详情见[官方平台要求](https://github.com/nodejs/node/blob/v24.21.0/BUILDING.md#platform-list)。Alpine 等使用 musl 的发行版不能直接照搬本文的安装包。

## 2. 下载并校验压缩包

以下步骤请在同一个 Bash 终端中依次执行，以保留设置的变量。这里以 x64 为例，ARM64 设备请将 `node_arch` 改为 `arm64`。

```bash
node_version=v24.21.0
node_arch=x64
node_dist="node-${node_version}-linux-${node_arch}"
download_dir="$HOME/Downloads/nodejs-${node_version}"

mkdir -p "$download_dir"
cd "$download_dir"

curl --fail --location --remote-name \
  "https://nodejs.org/dist/${node_version}/${node_dist}.tar.xz"
curl --fail --location --remote-name \
  "https://nodejs.org/dist/${node_version}/SHASUMS256.txt"
```

下载完成后，使用官方提供的 SHA-256 清单校验压缩包：

```bash
sha256sum --check --ignore-missing SHASUMS256.txt
```

确认所下载的 `.tar.xz` 文件显示 `OK` 后再继续。若下载或校验失败，应先排查原因。SHA-256 校验用于核对文件完整性；需要进一步验证校验清单的来源时，可按 [Node.js 官方说明验证签名](https://github.com/nodejs/node#verifying-binaries)。

## 3. 解压到安装目录

创建目录并解压：

```bash
sudo mkdir -p /usr/local/lib/nodejs
sudo tar -xJf "${node_dist}.tar.xz" -C /usr/local/lib/nodejs
```

本例的安装目录为 `/usr/local/lib/nodejs/node-v24.21.0-linux-x64`，其中 `bin` 目录包含 `node`、`npm` 和 `npx`。

## 4. 配置 Bash 环境变量

以下设置只针对当前用户的交互式 Bash 终端。在 `~/.bashrc` 末尾添加下面两行；版本和架构必须与实际解压的目录一致：

```bash
export NODEJS_HOME="/usr/local/lib/nodejs/node-v24.21.0-linux-x64"
export PATH="$NODEJS_HOME/bin:$PATH"
```

可以使用自己熟悉的编辑器，例如 `vim ~/.bashrc`。Vim 中按 `i` 进入插入模式，编辑完成后按 `Esc` 返回普通模式，再输入 `:wq` 保存退出。

让修改在当前 Bash 终端中生效：

```bash
source ~/.bashrc
hash -r
```

命令是 `source`，原文中的 `sorce` 是拼写错误。Bash 登录 Shell 是否加载 `~/.bashrc` 取决于登录配置文件；如有需要，在 `~/.bash_profile` 或 `~/.profile` 中显式加载它，具体规则见 [Bash 启动文件说明](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html)。Zsh、Fish 和 systemd 服务使用不同的配置方式，不能直接套用这个步骤。

## 5. 验证安装

检查实际调用的程序路径及版本：

```bash
command -v node
command -v npm
node -v
npm -v
npx -v
```

`node -v` 应输出 `v24.21.0`，程序路径应指向刚才安装目录中的 `bin`。如果仍然显示旧版本，可以用 `type -a node npm` 检查是否存在其他安装，以及 PATH 中的查找顺序。

以后升级时，可以将新版解压到新的版本目录，再修改 `NODEJS_HOME`。本文所述安装方式需要手动更新，不会自动跟随 Node.js 的安全补丁。
