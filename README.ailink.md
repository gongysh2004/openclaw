- [1. gateway 节点安装](#1-gateway-节点安装)
  - [1.1. 用户](#11-用户)
  - [1.2. 安装 Node.js](#12-安装-nodejs)
  - [1.3. 源代码安装](#13-源代码安装)
    - [1.3.1. 安装 pnpm（从源码构建时需要）](#131-安装-pnpm从源码构建时需要)
    - [1.3.2. 安装 Clawdbot](#132-安装-clawdbot)
  - [1.4. 自动安装](#14-自动安装)
  - [1.5. 配置](#15-配置)
  - [1.6. extra tools](#16-extra-tools)
    - [1.6.1. brew](#161-brew)
- [2. operator 节点](#2-operator-节点)
- [3. 被管理节点](#3-被管理节点)
  - [3.1. linux 服务器](#31-linux-服务器)
    - [3.1.1. 在Linux上执行](#311-在linux上执行)
    - [3.1.2. 在gateway节点上执行](#312-在gateway节点上执行)
  - [3.2. macos 笔记本](#32-macos-笔记本)
  - [3.3. android 手机](#33-android-手机)
- [4. 端口使用情况](#4-端口使用情况)
- [docker](#docker)

远程模式

# 1. gateway 节点安装

## 1.1. 用户

```
sudo useradd -m -s /bin/bash openclaw
sudo su - openclaw

```

## 1.2. 安装 Node.js

先使用 nvm 安装最新的 Node.js

```
 # 升级npm
 curl -o- https://gh-proxy.com/https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
 source ~/.bashrc
 # 设置 nvm 使用阿里云镜像
 export NVM_NODEJS_ORG_MIRROR=https://npmmirror.com/mirrors/node/
 nvm install 22
 nvm use 22
 nvm alias default 22

 # 验证 Node.js 版本：
node -v # Should print "v22.22.0".
# 验证 npm 版本：
npm -v # Should print "10.9.4".
```

## 1.3. 源代码安装

### 1.3.1. 安装 pnpm（从源码构建时需要）

```
# 方法1：使用 corepack（Node.js 22+ 内置，推荐）
# 设置 npm 使用阿里云镜像（corepack 会从此镜像下载 pnpm）
npm config set registry https://registry.npmmirror.com
corepack enable
npm config set registry https://registry.npmjs.org  # 恢复默认源（可选）

# 方法2：使用 npm 直接安装
npm config set registry https://registry.npmmirror.com
npm install -g pnpm
npm config set registry https://registry.npmjs.org  # 恢复默认源（可选）

# 验证 pnpm 版本：
pnpm -v
```

### 1.3.2. 安装 Clawdbot

```
git clone git@github.com:gongysh2004/openclaw.git
cd openclaw
pnpm install
pnpm ui:build # auto-installs UI deps on first run
pnpm build
pnpm setup
source ~/.bashrc 2>/dev/null || true
pnpm link --global

```

## 1.4. 自动安装

curl -fsSL https://molt.bot/install.sh | bash

## 1.5. 配置

```
export OPENCLAW_GATEWAY_TOKEN="bf73007211a2d94743b7aed6258a7724c0356a8af843f1b4"
openclaw onboard --install-daemon
openclaw gateway
openclaw dashboard --no-open



```

## 1.6. extra tools

### 1.6.1. brew

```
curl -fsSL https://gh-proxy.com/https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh | bash
```

# 2. operator 节点

```
ssh -N -L 18789:127.0.0.1:18789 gateway

```

使用browser 打开：
http://localhost:18789/?token=3719acd1015ee84ab5c3d05c233072ce498a1467dcc23b7a

利用chat 开始聊天，它会告诉你如何进行身份初始化。

# 3. 被管理节点

## 3.1. linux 服务器

### 3.1.1. 在Linux上执行

安装命令

```
git clone https://gh-proxy.com/https://github.com/openclaw/openclaw.git
cd openclaw
pnpm build
npm i -g .

```

建立ssh 隧道：

```
ssh -N -L 18789:127.0.0.1:18789 root@gateway
```

```
export OPENCLAW_GATEWAY_TOKEN="3719acd1015ee84ab5c3d05c233072ce498a1467dcc23b7a"
openclaw nodes run --host 127.0.0.1 --port 18789 --display-name "Linux1"
```

openclaw nodes run --host 127.0.0.1 --port 18789 --display-name "gpu3" --cwd ~/.openclaw3

### 3.1.2. 在gateway节点上执行

```
echo "192.168.99.113 linux1 Linux1" >> /etc/hosts
```

在gateway节点上配置Linux1的安全信息：

```
at > /tmp/exec-approvals-fixed.json << 'EOF'
{
  "version": 1,
  "socket": {
    "path": "/root/.openclaw/exec-approvals.sock",
    "token": "WxP7JdBIW5yAnxeb8-1f4h-GOm4idtt_"
  },
  "defaults": {
    "security": "full",
    "ask": "off",
    "askFallback": "full",
    "autoAllowSkills": true
  },
  "agents": {
    "main": {
      "security": "full",
      "ask": "off",
      "askFallback": "full",
      "autoAllowSkills": true
    }
  }
}
EOF
openclaw approvals set --node Linux1 --file /tmp/exec-approvals-fixed.json
```

openclaw approvals allowlist add --node Linux1 "df"

命令行执行命令：

```
openclaw nodes run --node Linux1 -- df -h
```

命令行下会需要审批，可在UI上批准。
![approval](approval.png)

## 3.2. macos 笔记本

## 3.3. android 手机

AVREKdDJyqq722SK

# 4. 端口使用情况

端口 用途
18789 Gateway WebSocket（默认，也是 HTTP 控制 UI
18792 端口 18792 是 Chrome Extension Relay Server 的端口

远程模式

node \*-- remote gateway

# docker

export OPENCLAW_HOME_VOLUME="openclaw_home"
./docker-setup.sh
