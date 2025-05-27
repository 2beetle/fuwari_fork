---
title: 解决在 Mac 上 Tailscale 和 Shadowrocket 无法同时使用的问题
published: 2025-05-27
description: 让 mac 在同时打开 Shadowrocket 和 tailscale 时，能够正常科学以及连接 tailscale 节点
image: ./cover.jpg
tags: [Tailscale, Shadowrocket, VPN]
category: Record
draft: false
---
# 背景
> 刚部署完 headscale 后当我的 mac 同时打开 Shadowrocket 和 tailscale 时，发现只能正常科学，但是无法连接到其他 tailscale 的节点

# 解决方案
1. 首先先确定自己 tailscale 的 IP 网段，我的是 headscale 默认的网段 `100.64.0.0/10` 
2. 在 Shadowrocket 中找到当前使用的配置，找到当前配置「TUN旁路路由」中的 headscale 网段
   ![config.png](images/config.png)
   ![config2.png](images/config2.png)
   ![delete_config.png](images/delete_config.png)
3. 从「TUN旁路路由」中删除该网段，点击保存
   ![delete.png](images/delete.png)
4. 现在可以同时使用，但是现在在 Shadowrocket 中 `100.64.0.0/10` 该网段为`代理`连接，我们可以在规则中加一个`IP-CIDR`规则，使其直连
   ![rule.png](images/rule.png)
   ![add_rule.png](images/add_rule.png)

# 注意事项
1. 完成上述修改后仍然不行的情况下，可以尝试重启电脑。

