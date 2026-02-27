很好 👍
我帮你整理成一份 **标准可复用的 OpenClaw Linux 安装文档**（基本已经是生产级流程了）。
你以后重装服务器可以直接照这个走。

---

# ✅ OpenClaw Debian 安装与配置流程（完整版）

适用于：

* Debian / Ubuntu
* 本地服务器 / VPS
* 飞书 Bot + OpenRouter LLM

---

# ① 创建专用用户 + systemd（⭐非常重要）

## 创建用户

```bash
sudo adduser openclaw
```

加入 sudo（可选）：

```bash
sudo usermod -aG sudo openclaw
```

切换用户：

```bash
su - openclaw
```

---

## 启用 systemd user 服务

回 root：

```bash
sudo loginctl enable-linger openclaw
```

作用：

* SSH 退出后仍运行
* 支持 systemd user service
* OpenClaw 可后台常驻

验证：

```bash
systemctl --user status
```

不再报：

```
Failed to connect to bus
```

✅ 成功。

---

# ② 安装最新版 Node.js（使用 nvm）

安装 nvm：

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

重新登录 shell。

---

安装 Node LTS（推荐）：

```bash
nvm install --lts
nvm alias default lts/*
```

验证：

```bash
node -v
npm -v
```

建议：

```
Node ≥ 20
```

---

# ③ 安装 OpenClaw（国内加速）

## 设置 npm 国内源

```bash
npm config set registry https://registry.npmmirror.com
npm config set audit false
npm config set fund false
npm cache clean --force
```

---

## 安装 OpenClaw

```bash
PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1 \
npm install -g openclaw
```

（避免 Chromium 下载卡死）

---

验证：

```bash
openclaw --version
```

---

# ④ 配置 OpenClaw（LLM：OpenRouter）

提前准备好LLM API key（比如OpenRouter免费模型）和飞书Bot。

首次启动生成配置：

```bash
openclaw onboard
```

按提示配置LLM和飞书。

生成：

```
~/.openclaw/openclaw.json
```
---

# ⑤ 配置飞书 Bot

进入：

👉 [https://open.feishu.cn](https://open.feishu.cn)

---

## 创建企业自建应用

步骤：

```
创建应用
→ 添加机器人能力
```

---

## 权限配置

添加：

* 接收消息
* 发送消息
* 获取用户信息

## 发布应用（v1.0.0）
* 发布为1.0.0版本：初始配置

---

## 事件与回调（需要先openclaw onboard里配置完成才能开启）

开启事件订阅：

* 使用 **长连接** 接收事件

然后添加事件
* im.message.receive_v1

---

## 在onboard完成后发布应用（v1.0.1）

* 发布为1.0.1版本：添加事件

```
版本管理 → 发布
```

否则机器人不会工作。

---

## 获取凭证

记录：

```
App ID
App Secret
```

---

## 写入 OpenClaw

在openclaw onboard里设置。

或者：

```sh
openclaw config set channels.feishu.appId "cli_a92bcd034378dbd2"
openclaw config set channels.feishu.appSecret "xxx"
```

---

# ⑥ Pair / Approve（允许私聊）

在飞书客户端里找到机器人，可以发消息给它。此时它会显示配对信息。

```sh
openclaw pairing list
openclaw pairing approve 482913
```
---

# ⑦ 启动方式（推荐 systemd）

OpenClaw 会自动安装 user service并启动。

检查：

```bash
systemctl --user status openclaw-gateway
```

启动（无需）：

```bash
systemctl --user start oopenclaw-gatewayw
```

开机自启（无需）：

```bash
systemctl --user enable openclaw-gateway
```

---

# ✅ 最终架构

```
Feishu
   ↓
OpenClaw
   ↓
OpenRouter
   ↓
LLM
```
