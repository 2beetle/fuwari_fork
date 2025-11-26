---
title: 使用rclone备份服务器数据
published: 2025-06-30
description: 使用rclone备份多个服务器数据到oss
tags: [Rclone, OSS]
category: Record
image: ./cover.jpg
draft: false
aiSummary: "本文详细介绍了使用 Rclone 工具将多个服务器数据备份到阿里云 OSS 的完整方案。从创建 RAM 权限策略和用户开始，逐步配置 Rclone 连接 OSS 存储，提供了完整的交互式配置流程。核心部分是一个功能完善的备份脚本，包含传参校验、错误检查、日志记录和文件排除规则，支持通过定时任务实现自动化备份，为多服务器环境提供了可靠的数据保护解决方案。"
aiSummaryGenerated: 2025-11-26
aiSummaryModel: "GLM-4.6"
---
# 配置阿里云
## 新增 RAM 权限策略
- https://ram.console.aliyun.com/policies/
```json
// backup_policy
{
  "Version": "1",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "oss:ListBuckets",
        "oss:ListObjects",
        "oss:DeleteObject",
        "oss:PutObject",
        "oss:GetObject"
      ],
      "Resource": [
        "acs:oss:oss-<regionId>:<accountId>:<bucketName>",
        "acs:oss:oss-<regionId>:<accountId>:<bucketName>/*"
      ]
    }
  ]
}
```

## 新增 RAM 用户
1. 为新增的 RAM 用户绑定上面新增的权限策略
2. 获取新增 RAM 用户的 access_key_id 和 secret_access_key

# 配置 rclone
1. [下载rclone](https://rclone.org/downloads/)
    ```sh
    sudo -v ; curl https://rclone.org/install.sh | sudo bash
    ```
2. 新增rclone config
    ```sh
    root@yellow-story-1:~# rclone config
    2025/06/30 03:48:49 NOTICE: Config file "/root/.config/rclone/rclone.conf" not found - using defaults
    No remotes found, make a new one?
    n) New remote
    s) Set configuration password
    q) Quit config
    n/s/q> n
    
    Enter name for new remote.
    name> alioss
    
    Option Storage.
    Type of storage to configure.
    Choose a number from below, or type in your own value.
    ...
     4 / Amazon S3 Compliant Storage Providers including AWS, Alibaba, ArvanCloud, Ceph, ChinaMobile, Cloudflare, DigitalOcean, Dreamhost, Exaba, FlashBlade, GCS, HuaweiOBS, IBMCOS, IDrive, IONOS, LyveCloud, Leviia, Liara, Linode, Magalu, Mega, Minio, Netease, Outscale, Petabox, RackCorp, Rclone, Scaleway, SeaweedFS, Selectel, StackPath, Storj, Synology, TencentCOS, Wasabi, Qiniu and others
       \ (s3)
    ...
    Storage> 4
    
    Option provider.
    Choose your S3 provider.
    Choose a number from below, or type in your own value.
    Press Enter to leave empty.
    ...
     2 / Alibaba Cloud Object Storage System (OSS) formerly Aliyun
       \ (Alibaba)
    ...
    provider> 2
    
    Option env_auth.
    Get AWS credentials from runtime (environment variables or EC2/ECS meta data if no env vars).
    Only applies if access_key_id and secret_access_key is blank.
    Choose a number from below, or type in your own boolean value (true or false).
    Press Enter for the default (false).
     1 / Enter AWS credentials in the next step.
       \ (false)
     2 / Get AWS credentials from the environment (env vars or IAM).
       \ (true)
    env_auth> (默认，直接回车)
    
    Option access_key_id.
    AWS Access Key ID.
    Leave blank for anonymous access or runtime credentials.
    Enter a value. Press Enter to leave empty.
    access_key_id> （输入刚刚获取的 access_key_id）
    
    Option secret_access_key.
    AWS Secret Access Key (password).
    Leave blank for anonymous access or runtime credentials.
    Enter a value. Press Enter to leave empty.
    secret_access_key> （输入刚刚获取的 secret_access_key）
    
    Option endpoint.
    Endpoint for OSS API.
    Choose a number from below, or type in your own value.
    Press Enter to leave empty.
     1 / Global Accelerate
       \ (oss-accelerate.aliyuncs.com)
     2 / Global Accelerate (outside mainland China)
       \ (oss-accelerate-overseas.aliyuncs.com)
     3 / East China 1 (Hangzhou)
       \ (oss-cn-hangzhou.aliyuncs.com)
     4 / East China 2 (Shanghai)
       \ (oss-cn-shanghai.aliyuncs.com)
     5 / North China 1 (Qingdao)
       \ (oss-cn-qingdao.aliyuncs.com)
     6 / North China 2 (Beijing)
       \ (oss-cn-beijing.aliyuncs.com)
     7 / North China 3 (Zhangjiakou)
       \ (oss-cn-zhangjiakou.aliyuncs.com)
     8 / North China 5 (Hohhot)
       \ (oss-cn-huhehaote.aliyuncs.com)
     9 / North China 6 (Ulanqab)
       \ (oss-cn-wulanchabu.aliyuncs.com)
    10 / South China 1 (Shenzhen)
       \ (oss-cn-shenzhen.aliyuncs.com)
    11 / South China 2 (Heyuan)
       \ (oss-cn-heyuan.aliyuncs.com)
    12 / South China 3 (Guangzhou)
       \ (oss-cn-guangzhou.aliyuncs.com)
    13 / West China 1 (Chengdu)
       \ (oss-cn-chengdu.aliyuncs.com)
    14 / Hong Kong (Hong Kong)
       \ (oss-cn-hongkong.aliyuncs.com)
    15 / US West 1 (Silicon Valley)
       \ (oss-us-west-1.aliyuncs.com)
    16 / US East 1 (Virginia)
       \ (oss-us-east-1.aliyuncs.com)
    17 / Southeast Asia Southeast 1 (Singapore)
       \ (oss-ap-southeast-1.aliyuncs.com)
    18 / Asia Pacific Southeast 2 (Sydney)
       \ (oss-ap-southeast-2.aliyuncs.com)
    19 / Southeast Asia Southeast 3 (Kuala Lumpur)
       \ (oss-ap-southeast-3.aliyuncs.com)
    20 / Asia Pacific Southeast 5 (Jakarta)
       \ (oss-ap-southeast-5.aliyuncs.com)
    21 / Asia Pacific Northeast 1 (Japan)
       \ (oss-ap-northeast-1.aliyuncs.com)
    22 / Asia Pacific South 1 (Mumbai)
       \ (oss-ap-south-1.aliyuncs.com)
    23 / Central Europe 1 (Frankfurt)
       \ (oss-eu-central-1.aliyuncs.com)
    24 / West Europe (London)
       \ (oss-eu-west-1.aliyuncs.com)
    25 / Middle East 1 (Dubai)
       \ (oss-me-east-1.aliyuncs.com)
    endpoint> （选取oss bucket的地域）
    
    （后面的配置默认即可）
    ```
3. 检测rclone是否配置成功
    ```sh
    rclone ls alioss:/<bucketName>
    ```

4. 新建备份脚本
    ```sh
    #!/bin/bash

    # ========================= 传参校验 =========================
    if [ -z "$1" ]; then
        echo "用法: $0 <machine_name>"
        exit 1
    fi
    
    MACHINE_NAME="$1"
    
    # ========================= 配置 =========================
    SOURCE_DIR="/root/projects"
    TARGET_DIR="alioss:/<bucketName>/backup/data/${MACHINE_NAME}/root/projects"
    LOG_FILE="/root/rclone/rclone_sync_log.txt"
    EXCLUDE_PATTERNS=(
        "--exclude" "/*/.well-known/"
        "--exclude" "/*/cache/"
        "--exclude" ".user.ini"
    )
    
    # ========================= 初始化日志文件 =========================
    mkdir -p "$(dirname "$LOG_FILE")"
    touch "$LOG_FILE"
    
    # ========================= 日志函数 =========================
    log_and_echo() {
        local time_str
        time_str=$(date "+%Y-%m-%d %H:%M:%S")
        echo "$time_str - $1" | tee -a "$LOG_FILE"
    }
    
    # ========================= 开始备份 =========================
    echo "======================== 开始 ========================" | tee -a "$LOG_FILE"
    log_and_echo "网站备份开始（机器名: $MACHINE_NAME）"
    
    # 检查源目录
    if [ ! -d "$SOURCE_DIR" ]; then
        log_and_echo "错误：源目录 $SOURCE_DIR 不存在"
        exit 1
    fi
    
    if [ ! -r "$SOURCE_DIR" ]; then
        log_and_echo "错误：源目录 $SOURCE_DIR 不可读"
        exit 1
    fi
    
    # 检查目标存储可达性
    if ! rclone lsd "$TARGET_DIR" > /dev/null 2>&1; then
        log_and_echo "错误：目标目录 $TARGET_DIR 不可访问或配置错误"
        exit 1
    fi
    
    # 执行同步备份
    log_and_echo "开始执行 rclone 同步到 $TARGET_DIR"
    rclone sync "$SOURCE_DIR" "$TARGET_DIR" -v "${EXCLUDE_PATTERNS[@]}" --delete-excluded --retries 10 >> "$LOG_FILE" 2>&1
    
    log_and_echo "网站备份结束"
    echo "======================== 结束 ========================" | tee -a "$LOG_FILE"
    ```
5. 定时运行备份脚本
   ```sh
   crontab -e
   # 0 3 * * * <your backup script path> <your machine name>
   ```
6. 查看运行日志
   ```sh
   tail -n 100 /root/rclone/rclone_sync_log.txt
   ```

# 参考文章
1. [rclone增量备份脚本](https://www.nodeseek.com/post-182756-1)