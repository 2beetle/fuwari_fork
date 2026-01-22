---
title: Mac使用Kiro Gate Way反代Kiro IDE模型
published: 2026-01-22
description: 在Mac中使用Kiro Gate Way反代Kiro IDE模型给Claude Code CLI使用
image: ./cover.jpg
tags: [Kiro, KiroGateWay, ClaudeCodeCLI, MacOS]
category: Service
draft: false
---

# 下载Kiro IDE并登录

1. [下载地址](https://kiro.dev/)
2. 打开IDE并登录

# 启动[kiro-gateway](https://github.com/jwadow/kiro-gateway)
1. 拉取仓库
   ```bash
   git clone https://github.com/Jwadow/kiro-gateway.git && cd kiro-gateway
   ```
   
2. 配置虚拟环境，本文章使用的环境管理工具是[UV](https://docs.astral.sh/uv/)
   ```bash
   uv venv && source .venv/bin/activate && uv pip install -r requirements.txt
   ```

3. 编辑配置
   ```bash
   cp .env.example .env && vim .env
   ```
   ```dotenv
   ...
   PROXY_API_KEY="<配置你自己的apikey，用于Claude Code验证>"

   # ===========================================
   # OPTION 1: Kiro IDE credentials (JSON file)
   # ===========================================
   
   # Path to JSON credentials file from Kiro IDE
   KIRO_CREDS_FILE="~/.aws/sso/cache/kiro-auth-token.json" # IDE授权文件路径
   ...
   ```
   
4. 安装supervisor
   ```bash
   brew install supervisor
   ```
   ```bash
   brew services start supervisor
   ```
   
5. 配置 [kiro-gateway](https://github.com/jwadow/kiro-gateway) 服务到supervisor并启动
   ```bash
   mkdir -p /opt/homebrew/etc/supervisor.d/ && cd /opt/homebrew/etc/supervisor.d/
   ```
   ```bash
   vim kiro-gateway.ini
   ```
   ```ini
   [program:kiro-gateway]
   directory=<kiro-gateway项目路径>
   command=<kiro-gateway项目路径>/.venv/bin/python main.py --port 8989
   
   autostart=true
   autorestart=true
   startsecs=3
   stopasgroup=true
   killasgroup=true
   
   stdout_logfile=/tmp/kiro-gateway.out.log
   stderr_logfile=/tmp/kiro-gateway.err.log
   stdout_logfile_maxbytes=50MB
   stderr_logfile_maxbytes=50MB
   stdout_logfile_backups=5
   stderr_logfile_backups=5
   
   user=<user>
   environment=PYTHONUNBUFFERED="1"
   ```
   ```bash
   supervisorctl reread && supervisorctl update && tail -f /tmp/kiro-gateway.out.log
   ```

# 配置Claude Code CLI环境变量
   ```bash
   vim ~/.claude/settings.json
   ```
   ```json
   {
     "env": {
       "ANTHROPIC_AUTH_TOKEN": "{PROXY_API_KEY}",
       "ANTHROPIC_MODEL": "",
       "ANTHROPIC_DEFAULT_SONNET_MODEL": "",
       "ANTHROPIC_DEFAULT_OPUS_MODEL": "",
       "ANTHROPIC_DEFAULT_HAIKU_MODEL": "",
       "ANTHROPIC_BASE_URL": "http://localhost:8989",
       "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
     },
     "codemossProviderId": "xxxxxxx"
   }
   ```

