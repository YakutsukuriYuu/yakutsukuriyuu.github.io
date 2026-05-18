+++
title = 'frp内网穿透部署教程'
date = '2026-05-18T12:00:00+08:00'
draft = false
categories = ["教程"]
tags = ["frp", "内网穿透", "linux"]
author = 'Yuu'
+++

## 什么是 frp

frp（Fast Reverse Proxy）是一个高性能的反向代理应用，用于内网穿透。通过具有公网 IP 的服务器作为中转，可以将内网服务暴露到公网访问。

## 环境说明

| 角色 | 说明 |
|------|------|
| 服务器（frps） | 有公网 IP 的云服务器，作为中转 |
| 客户端（frpc） | 内网机器，需要暴露服务的设备 |

本文以 Linux 系统为例，当前 frp 最新版本为 0.68.1。

## 安装 frp

在服务器和客户端都执行以下步骤：

```bash
# 下载最新版本（替换为实际版本号）
wget https://github.com/fatedier/frp/releases/download/v0.68.1/frp_0.68.1_linux_amd64.tar.gz

# 解压
tar -xzf frp_0.68.1_linux_amd64.tar.gz

# 移动到 /opt 目录
sudo mv frp_0.68.1_linux_amd64 /opt/frp
```

## 服务端配置（frps）

编辑 `/opt/frp/frps.toml`：

```toml
bindPort = 7000
```

`bindPort` 是 frp 服务端监听的端口，用于客户端连接。

## 客户端配置（frpc）

编辑 `/opt/frp/frpc.toml`：

```toml
serverAddr = "your-server-ip"
serverPort = 7000

auth.method = "token"
auth.token = "your-secure-token"

[[proxies]]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22
remotePort = 6005

[[proxies]]
name = "web-service"
type = "tcp"
localIP = "127.0.0.1"
localPort = 8080
remotePort = 6006
```

- `serverAddr`：替换为你的云服务器公网 IP
- `auth.token`：替换为你自己设定的密钥，服务端和客户端需一致
- `[[proxies]]`：每个代理规则对应一个需要穿透的服务
  - `name`：代理名称，自定义
  - `type`：协议类型，tcp / udp / http / https
  - `localIP` / `localPort`：本地服务地址和端口
  - `remotePort`：映射到服务器上的端口

> 如果服务端也要设置 token 验证，在 `frps.toml` 中添加：
> ```toml
> auth.method = "token"
> auth.token = "your-secure-token"
> ```

## 配置 systemd 开机自启

### 服务端

```bash
sudo tee /etc/systemd/system/frps.service << 'EOF'
[Unit]
Description=FRP Server Service
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/opt/frp/frps -c /opt/frp/frps.toml
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now frps
```

### 客户端

```bash
sudo tee /etc/systemd/system/frpc.service << 'EOF'
[Unit]
Description=FRP Client Service
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/opt/frp/frpc -c /opt/frp/frpc.toml
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now frpc
```

## 防火墙配置

服务器需要开放 frp 监听端口以及所有映射的 remotePort：

```bash
# frp 自身端口
sudo ufw allow 7000/tcp

# 映射的服务端口
sudo ufw allow 6005/tcp
sudo ufw allow 6006/tcp
```

如果用的是云服务器，还需要在云服务商的安全组中放行对应端口。

## 验证

```bash
# 查看服务状态
sudo systemctl status frpc
sudo systemctl status frps

# 查看日志
journalctl -u frpc -f
```

在客户端通过 SSH 测试连接：

```bash
# 从外网通过服务器端口访问内网机器
ssh user@your-server-ip -p 6005
```

## 常用管理命令

```bash
sudo systemctl start frpc      # 启动客户端
sudo systemctl stop frpc       # 停止客户端
sudo systemctl restart frpc    # 重启客户端
sudo systemctl status frpc     # 查看状态
journalctl -u frpc -f          # 实时日志
```

## 排错记录

### token 不匹配

客户端和服务端的 `auth.token` 必须一致，否则连接会被拒绝。日志中会提示 `token in login doesn't match token from configuration`。

### 端口已被占用

`bindPort` 或 `remotePort` 被其他服务占用的报错为 `bind: address already in use`。用以下命令检查端口占用：

```bash
sudo ss -tlnp | grep <端口号>
```

### 云服务器端口不通

检查云服务商安全组规则是否放行了对应端口，这是最容易被忽略的一步。
