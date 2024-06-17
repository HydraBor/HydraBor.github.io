---
title: 关于在 Linux 下安装 Node.js
date: 2024-06-17 21:16:40
tags: 
    - Node.js
    - Linux
    - 随笔
---
## 本文为在 Linux 系统下使用压缩包安装 Node.js(v20.14.0) 详细流程

### 新建目录

在 `/usr/local/lib/` 目录下创建一个空目录用来存放 Node.js 源文件，我这里将其命名为 `node`：

```bash
sudo mkdir /usr/local/lib/node
```

### 下载压缩包

访问 [Node.js](https://nodejs.org/en/download/prebuilt-binaries) 官网选择合适的版本、系统、架构，本文以 `v20.14.0-Linux-x64` 为例

切换到刚才新建的目录下，使用 `wget` 命令下载压缩包：

```bash
cd /usr/local/lib/node
sudo wget https://nodejs.org/dist/v20.14.0/node-v20.14.0-linux-x64.tar.xz
```

### 解压

使用 `tar` 命令解压到当前目录：

```bash
sudo tar xf node-v20.14.0-linux-x64.tar.xz 
```

解压完毕后可以选择删除压缩包：

```bash
sudo rm node-v20.14.0-linux-x64.tar.xz 
```

### 添加到环境变量

在 `/etc/profile` 目录末尾加上这样两句话：

```bash
export NODEJS_HOME=/usr/local/lib/node/node-v20.14.0-linux-x64
export PATH=$NODEJS_HOME/bin:$PATH
```

终端操作下使用 `vim` 编辑器可以对上述文本进行编辑：

```bash
sudo vim /etc/profile
```

按 `i` 进入编辑模式，编辑完成后按 `Esc` 回到命令行模式，输入 `:wq` 保存退出

使用 `sorce` 命令启用：

```bash
sorce /etc/profile
```

### 完成安装

输入命令检查是否完成安装：

```bash
node -v
npm -v
```
