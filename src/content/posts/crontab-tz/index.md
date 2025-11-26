---
title: ubuntu中crontab时区调整
published: 2024-11-14
description: crontab设置定时运行后因为时区问题导致运行时间与预想不一致
tags: [Crontab,Timezone]
category: Record
image: ./images/crontab.png
draft: false
aiSummary: "本文解决了 Ubuntu 系统中 crontab 定时任务因时区问题导致的执行时间不准确问题。提供了一键修复脚本，通过安装时区数据、设置中国时区、启用网络时间同步并重启 cron 服务，确保定时任务能够按照预期的北京时间准确执行。这个解决方案对于需要在服务器上部署定时任务的中国用户非常实用。"
aiSummaryGenerated: 2025-11-26
aiSummaryModel: "GLM-4.6"
---
# 一键运行
```bash
apt install -y tzdata && timedatectl set-timezone Asia/Shanghai && timedatectl && timedatectl set-ntp true  && service cron restart
```