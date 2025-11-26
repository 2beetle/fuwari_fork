---
title: Nginx 踩坑
published: 2025-08-13
description: 记录一些使用nginx时踩坑的情况
image: ./cover.jpg
tags: [Nginx]
category: Record
draft: false

# AI Summary
aiSummary: "本文详细记录了一次 Nginx 配置 Django 项目时遇到的无限重定向问题。问题出现在 Django 的 SSL 重定向配置与 Nginx 代理配置不匹配：Django 设置了 SECURE_SSL_REDIRECT=True 强制 HTTPS，但 Nginx 缺少 proxy_set_header X-Forwarded-Proto $scheme 配置，导致 Django 无法识别真实请求协议。解决方案是在 Nginx 配置中添加该关键配置，让后端能够正确识别客户端的访问协议。"
aiSummaryGenerated: 2025-11-26
aiSummaryModel: "Claude Sonnet"
---

# Nginx无限重定向

## 背景
在为 Django 项目配置好 nginx 后，测试外网请求时无限显示 301

```log
...
backend-1     | 172.23.0.1 - - [05/Aug/2025:15:20:58 +0000] "PUT /api/v1.0/<url>/ HTTP/1.0" 301 0 "<url>" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36"
backend-1     | 172.23.0.1 - - [05/Aug/2025:15:20:58 +0000] "PUT /api/v1.0/<url>/ HTTP/1.0" 301 0 "<url>" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36"
backend-1     | 172.23.0.1 - - [05/Aug/2025:15:20:58 +0000] "PUT /api/v1.0/<url>/ HTTP/1.0" 301 0 "<url>" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36"
...
```

## 问题定位
### Django 配置
```python
# Django 会检测当前请求是否是 HTTPS，如果不是，就会重定向到 HTTPS
SECURE_PROXY_SSL_HEADER = ("HTTP_X_FORWARDED_PROTO", "https")
SECURE_SSL_REDIRECT = True
```

### Nginx 配置
```nginx
location / {
    proxy_pass http://127.0.0.1:8000;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    # proxy_set_header X-Forwarded-Proto $scheme;  # 缺少此项关键配置
}
```

### 请求流程
1. 浏览器（HTTPS） → Nginx（HTTPS）
2. Nginx（HTTP） → Django（HTTP）
3. Django 判断不是 HTTPS → 重定向到 HTTPS
4. 浏览器再走一次 1 → 2 → 3 循环往复，形成无限重定向。

## 问题解决
在nginx配置中添加关键配置
```nginx
proxy_set_header X-Forwarded-Proto $scheme; #  $scheme 会根据客户端访问协议自动填充为 http 或 https。
```
