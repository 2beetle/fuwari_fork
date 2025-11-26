---
title: 管理 debian 日志大小
published: 2025-07-09
description: 减少日志文件占用的磁盘大小
image: ./cover.jpg
tags: [Logrotate, Journald]
category: Record
draft: false
aiSummary: "本文全面介绍了在 Debian 系统中管理日志文件大小的两种主要方法：使用 logrotate 管理传统日志文件和配置 journald 管理系统日志。通过实际案例展示了如何将 daemon.log 从 1.2GB 减少到合理大小，以及如何将 journald 日志从 3.9GB 清理到 200MB。文章详细说明了各种配置参数的作用，并解答了常见的日志清理问题，为系统管理员提供了实用的磁盘空间管理方案。"
aiSummaryGenerated: 2025-11-26
aiSummaryModel: "GLM-4.6"
---
# 安装依赖
1. 安装`logrotate`
    ```bash
    sudo apt update
    ```
    ```bash
    sudo apt install logrotate
    ```
   
# 使用logrotate管理日志大小
## 配置文件路径说明
1. `logrotate`的主配置文件在`/etc/logrotate.conf`
2. `logrotate`的子配置文件在`/etc/logrotate.d/`

## 编辑配置文件
1. 原来日志占用大小
    ```shell
    ls -llh |grep daemon.log
    #-rw-r-----  1 root    adm             469M Jul  8 21:43 daemon.log
    #rw-r-----  1 root    adm             1.2G Jul  6 00:00 daemon.log.1
    #-rw-r-----  1 root    adm              40M Jun 29 00:00 daemon.log.2.gz
    #-rw-r-----  1 root    adm              40M Jun 22 00:00 daemon.log.3.gz
    #-rw-r-----  1 root    adm              40M Jun 15 00:00 daemon.log.4.gz
    ```
   
2. 修改
    ```shell
    sudo vim /etc/logrotate.d/rsyslog
    ```
    ```conf
    # ...其他配置文件
    /var/log/daemon.log
    {
        size 5M
        rotate 5
        compress
        missingok
        notifempty
        delaycompress
        postrotate
            /usr/lib/rsyslog/rsyslog-rotate
        endscript
    }
    ```
   - 参数说明：
     - `size 5M`：单个日志文件最大为 5MB。

     - `rotate 5`：最多保留 5 个轮转文件。

     - `compress`：旧文件压缩为 .gz。

     - `delaycompress`：推迟一轮压缩，以防被新进程写入。

     - `notifempty`：若文件为空则不轮转。

     - `missingok`：文件不存在不会报错。

     - `postrotate`：轮转后重启 rsyslog。 

3. 测试配置
    ```shell
    sudo logrotate -d /etc/logrotate.conf     # 测试模式，不实际执行
    ```
    ```shell
    sudo logrotate -f /etc/logrotate.conf     # 强制执行一次
    ```

4. 现在日志占用大小
    ```shell
    ls -llh |grep daemon.log
    #-rw-r-----  1 root    adm              52K Jul  8 21:54 daemon.log
    #-rw-r-----  1 root    adm             470M Jul  8 21:54 daemon.log.1
    #-rw-r-----  1 root    adm              40M Jul  6 00:00 daemon.log.2.gz
    #-rw-r-----  1 root    adm              40M Jun 29 00:00 daemon.log.3.gz
    #-rw-r-----  1 root    adm              40M Jun 22 00:00 daemon.log.4.gz
    #-rw-r-----  1 root    adm              40M Jun 15 00:00 daemon.log.5.gz
    ```
   
# 管理journald日志大小
1. 查看管理前`journald`占用日志大小
    ```shell
    sudo journalctl --disk-usage
    # Archived and active journals take up 3.9G in the file system.
    ```

2. 编辑配置文件
    ```shell
    sudo vim /etc/systemd/journald.conf
    ```
    ```conf
    [Journal]
    SystemMaxUse=200M
    SystemMaxFileSize=50M
    SystemKeepFree=100M
    MaxRetentionSec=1month
    Compress=yes
    ```
   - 参数说明：
     - `SystemMaxUse=200M`	/var/log/journal 总大小上限
     - `SystemMaxFileSize=50M`	单个日志文件最大 50MB
     - `SystemKeepFree=100M`	至少保留 100MB 可用磁盘空间
     - `MaxRetentionSec=1month`	日志最多保留一个月
     - `Compress=yes`	启用压缩，节省磁盘空间（默认已启用）

3. 重启`journald`服务
    ```shell
    sudo systemctl restart systemd-journald
    ```

4. 清理已有`journald`日志
    ```shell
    sudo journalctl --vacuum-size=200M
    ```

5. 查看当前`journald`占用日志大小
    ```shell
    sudo journalctl --disk-usage
    # Archived and active journals take up 240.2M in the file system.
    ```
   
# 常见问题
1. 为什么执行logrotate清理日志后旧文件不会被`5M`限制？
    - size`5M`只控制“当前文件何时触发轮转”，并不限制 .1、.2 等旧文件的大小
2. 为什么清理完`journald`日志后日志占用磁盘还是超过`200M`？
    - `--vacuum-size=200M` 的作用不是让日志压缩到“最多 200M”，而是：“清除归档日志（archived journals），直到归档部分的总大小小于 200M”。但 “活动日志”（active journal）不会被清除，只会保留当前 journald 正在写入的日志文件。
    ```
    /var/log/journal/
      └─ <MACHINE-ID>/
          ├─ system@0005xxxx.journal (归档)
          ├─ system@0006xxxx.journal (归档)
          └─ system.journal            (当前活动日志)
    ```


    