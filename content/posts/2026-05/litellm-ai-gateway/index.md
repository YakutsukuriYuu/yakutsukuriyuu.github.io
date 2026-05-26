---
title: "用 LiteLLM Proxy 搭建统一 AI 网关"
date: 2026-05-19
draft: false
tags: ["AI", "LiteLLM", "DeepSeek", "MiMo", "Gateway"]
categories: ["教程"]
ShowToc: true
TocOpen: true
---

## 背景

手上有多个 AI 模型 API 时，每个提供商有不同的 Key、不同的 Endpoint，管理起来很麻烦。LiteLLM Proxy 可以把它们统一成一个 OpenAI 兼容的入口，改个 `model` 参数就能随意切换。

本文在一台 Ubuntu 22.04 服务器上搭建，接入了**小米 MiMo**和**DeepSeek**两条线路。

## 最终效果

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:4000/v1",
    api_key="sk-4871e..."
)

# 小米
client.chat.completions.create(model="mimo-v2.5-pro", ...)
# DeepSeek
client.chat.completions.create(model="deepseek-v4-pro", ...)
```

同一个端点，改 `model` 参数就能切。**Claude Code 走 Anthropic passthrough 连 DeepSeek，代码调用走 OpenAI 端口切任意模型。**

## 架构

```
代码调用 (OpenAI SDK)
       │
       ▼  /v1/chat/completions
┌──────────────────────┐
│  LiteLLM Proxy :4000 │
│  - /v1/*  (路由模式)  │
│  - /anthropic/* (透传)│
└──┬─────────────┬─────┘
   │             │
   ▼             ▼
┌──────┐   ┌──────────┐
│小米   │   │ DeepSeek │
│token-│   │ api.     │
│plan- │   │ deepseek │
│cn... │   │ .com     │
└──────┘   └──────────┘
```

## 环境

| 项目 | 详情 |
|------|------|
| OS | Ubuntu 22.04 LTS |
| Python | 3.10.12 |
| 包管理 | uv 0.11 |
| 进程管理 | systemd --user |
| 目录 | `~/.opt/litellm/` |

无 Docker，无 PostgreSQL，纯 Python + SQLite（实际无 DB 运行）。

## 安装

### 1. 创建目录和虚拟环境

```bash
mkdir -p ~/.opt/litellm
cd ~/.opt/litellm
uv venv --python 3.10
source .venv/bin/activate
uv pip install 'litellm[proxy]' prisma
```

### 2. 创建 `.env`

```bash
# ~/.opt/litellm/.env
ANTHROPIC_API_KEY=sk-bd73c6dd6a124923ac287093ad036c97
ANTHROPIC_API_BASE=https://api.deepseek.com/anthropic
XIAOMI_API_KEY=tp-c3xfr575rjwi9dtch555xdrzo2xkr0awmxmuunofcolqtr72
```

- `ANTHROPIC_API_KEY` / `ANTHROPIC_API_BASE` — 给 Anthropic passthrough 用的 DeepSeek 凭证
- `XIAOMI_API_KEY` — 小米 API Key（`tp-` 格式）

### 3. 创建 `litellm_config.yaml`

```yaml
# ~/.opt/litellm/litellm_config.yaml
general_settings:
  master_key: os.environ/LITELLM_MASTER_KEY

model_list:
  # ========== 小米 MiMo (OpenAI 兼容) ==========
  - model_name: mimo-v2.5-pro
    litellm_params:
      model: openai/mimo-v2.5-pro
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2.5
    litellm_params:
      model: openai/mimo-v2.5
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2-pro
    litellm_params:
      model: openai/mimo-v2-pro
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2-omni
    litellm_params:
      model: openai/mimo-v2-omni
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2-flash
    litellm_params:
      model: openai/mimo-v2-flash
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2.5-tts
    litellm_params:
      model: openai/mimo-v2.5-tts
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2.5-tts-voiceclone
    litellm_params:
      model: openai/mimo-v2.5-tts-voiceclone
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2.5-tts-voicedesign
    litellm_params:
      model: openai/mimo-v2.5-tts-voicedesign
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1
  - model_name: mimo-v2-tts
    litellm_params:
      model: openai/mimo-v2-tts
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/v1

  # ========== 小米 MiMo (Anthropic 兼容) ==========
  - model_name: mimo-v2.5-pro-anthropic
    litellm_params:
      model: anthropic/mimo-v2.5-pro
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/anthropic/v1/messages
  - model_name: mimo-v2.5-anthropic
    litellm_params:
      model: anthropic/mimo-v2.5
      api_key: os.environ/XIAOMI_API_KEY
      api_base: https://token-plan-cn.xiaomimimo.com/anthropic/v1/messages

  # ========== DeepSeek (Anthropic 兼容) ==========
  - model_name: deepseek-v4-pro
    litellm_params:
      model: anthropic/deepseek-v4-pro[1m]
      api_key: os.environ/ANTHROPIC_API_KEY
      api_base: https://api.deepseek.com/anthropic/v1/messages
  - model_name: deepseek-v4-flash
    litellm_params:
      model: anthropic/deepseek-v4-flash
      api_key: os.environ/ANTHROPIC_API_KEY
      api_base: https://api.deepseek.com/anthropic/v1/messages

litellm_settings:
  drop_params: true
  set_verbose: false

router_settings:
  num_retries: 3
  allowed_fails: 5
  cooldown_time: 30
```

每个模型的 `litellm_params` 关键字段：

- `model` — 提供商前缀 + 模型 ID（`openai/` 或 `anthropic/`）
- `api_key` — 密钥来源（`os.environ/XXX` 读取环境变量）
- `api_base` — 实际 API 端点

### 4. 创建 systemd 用户服务

```ini
# ~/.config/systemd/user/litellm.service
[Unit]
Description=LiteLLM AI Gateway
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
WorkingDirectory=/home/qiansu/.opt/litellm
EnvironmentFile=/home/qiansu/.opt/litellm/.env
Environment="LITELLM_MASTER_KEY=sk-4871e6817dd961100f1a1e6fab540bfdea12f464f55da6ef"
ExecStart=/home/qiansu/.opt/litellm/.venv/bin/litellm --config /home/qiansu/.opt/litellm/litellm_config.yaml --port 4000
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=default.target
```

启动：

```bash
systemctl --user daemon-reload
systemctl --user enable litellm
systemctl --user start litellm
```

### 5. 开机自启（可选）

用户级 systemd 默认登录后才启动。如需无登录自启：

```bash
sudo loginctl enable-linger $USER
```

## 验证

```bash
# 服务状态
systemctl --user status litellm

# 模型列表
curl -s http://localhost:4000/v1/models \
  -H "Authorization: Bearer sk-4871e..."

# 调用小米
curl -s -X POST http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer sk-4871e..." \
  -H "Content-Type: application/json" \
  -d '{"model":"mimo-v2.5-pro","max_tokens":100,"messages":[{"role":"user","content":"你好"}]}'

# 调用 DeepSeek (Anthropic 端口)
curl -s http://localhost:4000/anthropic/v1/messages \
  -H "x-api-key: sk-4871e..." \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-pro[1m]","max_tokens":100,"messages":[{"role":"user","content":"你好"}]}'
```

## Claude Code 配置

在 `~/.zshrc` 中：

```bash
# Claude Code use LiteLLM Gateway -> DeepSeek
export ANTHROPIC_BASE_URL="http://localhost:4000/anthropic"
export ANTHROPIC_AUTH_TOKEN="sk-4871e6817dd961100f1a1e6fab540bfdea12f464f55da6ef"
export ANTHROPIC_MODEL="deepseek-v4-pro[1m]"
export ANTHROPIC_DEFAULT_OPUS_MODEL="deepseek-v4-pro[1m]"
export ANTHROPIC_DEFAULT_SONNET_MODEL="deepseek-v4-pro[1m]"
export ANTHROPIC_DEFAULT_HAIKU_MODEL="deepseek-v4-flash"
export CLAUDE_CODE_SUBAGENT_MODEL="deepseek-v4-flash"
export CLAUDE_CODE_EFFORT_LEVEL="max"
```

Claude Code 内切换模型用 `/model` 命令：

```
/model deepseek-v4-pro[1m]
/model deepseek-v4-flash
```

## 日常运维

```bash
# 查看日志
journalctl --user -u litellm -f

# 重启
systemctl --user restart litellm

# 修改配置后热重载
# 注意：litellm 不支持热重载，需要重启
systemctl --user restart litellm
```

## 端点说明

| 端点 | 路由方式 | 可用模型 | 适用场景 |
|------|----------|----------|----------|
| `/v1/chat/completions` | 按 model 名查路由表 | 全部 13 个 | Python SDK, curl, 代码调用 |
| `/anthropic/v1/messages` | passthrough 直传 | DeepSeek 两个 | Claude Code |

**限制**：Anthropic 端点只支持单目标透传（当前指向 DeepSeek），不支持按 model 名切小米——这是 litellm 1.85 的限制。

## 踩坑记录

1. **Prisma 模块缺失** — 安装后 `uv pip install prisma` 并在工作目录运行 `prisma generate`
2. **SQLite 不支持** — litellm 1.85 的 Prisma schema 只支持 PostgreSQL，不设 `database_url` 跳过
3. **API 地址写错** — 小米官网 `api.xiaomimimo.com`，实际用的 `token-plan-cn.xiaomimimo.com`
4. **模型名大小写** — 小米 API 模型名是全小写 `mimo-v2.5-pro`，不是 `MiMo-V2.5-Pro`
5. **Key 格式** — 小米 `tp-` 前缀，DeepSeek `sk-` 前缀，注意区分
6. **Anthropic 端点不路由** — `/anthropic/v1/messages` 是透传不是路由，换模型需切 `ANTHROPIC_API_BASE`

## 扩展方向

- **PostgreSQL** — 接入数据库后可启用用量统计、Virtual Keys、速率限制
- **OpenAI 接入** — 在 model_list 加上 `api.openai.com` 即可
- **本地模型** — Ollama/vLLM 接入，适用于离线场景
- **Prometheus 监控** — LiteLLM 自带 `/metrics` 端点
