---
title: nginx缓存dns信息
published: 2024-12-03
description: 家里云使用ddns解析ipv6，在vps nginx上使用proxy_pass解析域名，ipv6发生改变后vps未能解析域名到正确ipv6
image: ./cover.jpg
tags: [Nginx]
category: Record
draft: false
aiSummary: "本文解决了 Nginx 缓存 DNS 解析导致的动态 IPv6 地址更新问题。当家庭云使用 DDNS 解析 IPv6 地址时，VPS 上的 Nginx 会缓存域名解析结果，导致 IPv6 地址变更后无法正确连接。提供了两种解决方案：简单的重启服务方法，以及更彻底的编译安装 nginx-upstream-dynamic-servers 模块实现动态域名解析。详细记录了从源码编译安装 Nginx 的完整流程和配置方法。"
aiSummaryGenerated: 2025-11-26
aiSummaryModel: "GLM-4.6"
---

# 查看nginx错误日志
- 发现nginx将域名解析到了旧ipv6地址
    ```bash
    tail -n 100 /var/log/nginx/error.log
    
    # 2024/12/03 09:28:49 [error] 2371661#2371661: *39731 connect() failed (113: No route to host) while connecting to upstream, client: 162.158.170.60, server: xxx.xxx.net, request: "GET /web/index.html HTTP/2.0", upstream: "https://[2409:xxxx:14e2:xxxx:84:6d5a:3531:xxxx]:<port>/web/index.html", host: "xxx.xxx.net"
    ```

# 解决方法1
- 重启nginx
    ```bash
    seervice nginx restart
    ```

# 解决方法2
- 使用```nginx-upstream-dynamic-servers```

1. 安装依赖
    ```bash
    apt install -y build-essential && apt install -y libpcre3 libpcre3-dev && apt install -y zlib1g-dev && apt install -y openssl libssl-dev
    ```

2. 下载nginx源码
    ```bash
    cd /usr/local/src && wget https://nginx.org/download/nginx-1.26.2.tar.gz && tar -xf nginx-1.26.2.tar.gz && cd nginx-1.26.2
    ```
3. 下载`nginx-upstream-dynamic-servers`
    ```bash
    cd /usr/src && git clone https://github.com/GUI/nginx-upstream-dynamic-servers.git
    ```
4. 配置和编译
   ```bash
    cd /usr/local/src/nginx-1.26.2 && ./configure \
    --prefix=/usr/local/nginx \
    --user=www \
    --group=www \
    --sbin-path=/usr/local/sbin/nginx \
    --conf-path=/usr/local/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --lock-path=/var/run/nginx.lock \
    --http-client-body-temp-path=/var/cache/nginx/client_temp \
    --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
    --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
    --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
    --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
    --with-file-aio \
    --with-threads \
    --with-http_addition_module \
    --with-http_auth_request_module \
    --with-http_dav_module \
    --with-http_flv_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_mp4_module \
    --with-http_random_index_module \
    --with-http_realip_module \
    --with-http_secure_link_module \
    --with-http_slice_module \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-http_sub_module \
    --with-http_v2_module \
    --with-mail \
    --with-mail_ssl_module \
    --with-stream \
    --with-stream_realip_module \
    --with-stream_ssl_module \
    --with-stream_ssl_preread_module \
    --add-module=/usr/src/nginx-upstream-dynamic-servers
    ```
   - 编译
   ```bash
   make
   ```
5. 安装
    ```bash
    make install
    ```
6. 创建守护进程
    1. 编写进程配置
       ```bash
       vim /usr/lib/systemd/system/nginx.service
       ```
       ```conf
       [Unit]
       Description=A high performance web server and a reverse proxy server
       Documentation=man:nginx(8)
       After=network.target
    
       [Service]
       Type=forking
       PIDFile=/var/run/nginx.pid
       ExecStartPre=/usr/local/sbin/nginx -t -q -g 'daemon on; master_process on;'
       ExecStart=/usr/local/sbin/nginx
       ExecReload=/usr/local/sbin/nginx -s reload
       ExecStop=/usr/local/sbin/nginx -s quit
       TimeoutStopSec=5
       KillMode=mixed
       PrivateTmp=true
    
       [Install]
       WantedBy=multi-user.target
       ```
    2. 加载守护进程
       ```bash
       systemctl daemon-reload && systemctl start nginx
       ```
       
7. 使用`dig`查看dns服务器
    ```bash
    $ dig
    
    #; <<>> DiG 9.18.28-0ubuntu0.20.04.1-Ubuntu <<>>
    #;; global options: +cmd
    #;; Got answer:
    #;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 61540
    #;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1
    #
    #;; OPT PSEUDOSECTION:
    #; EDNS: version: 0, flags:; udp: 65494
    #;; QUESTION SECTION:
    #;.                              IN      NS
    #
    #;; ANSWER SECTION:
    #.                       515820  IN      NS      a.root-servers.net.
    #.                       515820  IN      NS      b.root-servers.net.
    #.                       515820  IN      NS      c.root-servers.net.
    #.                       515820  IN      NS      d.root-servers.net.
    #.                       515820  IN      NS      e.root-servers.net.
    #.                       515820  IN      NS      f.root-servers.net.
    #.                       515820  IN      NS      g.root-servers.net.
    #.                       515820  IN      NS      h.root-servers.net.
    #.                       515820  IN      NS      i.root-servers.net.
    #.                       515820  IN      NS      j.root-servers.net.
    #.                       515820  IN      NS      k.root-servers.net.
    #.                       515820  IN      NS      l.root-servers.net.
    #.                       515820  IN      NS      m.root-servers.net.
    #
    #;; Query time: 4 msec
    #;; SERVER: 127.0.0.xxx#53(127.0.0.xxx) (UDP)
    #;; WHEN: Tue Dec 03 13:11:58 CST 2024
    #;; MSG SIZE  rcvd: 239
    ```
8. 修改nginx配置文件
    ```bash 
    vim /usr/local/nginx/nginx.conf
    ```
    ```nginx
    # 将以下配置添加到nginx配置文件
    http {
      resolver 127.0.0.xxx;
    
      upstream example {
        server example.com resolve;
      }
    }
    ```

9. 检查nginx配置文件并重启nginx
    ```bash 
    nginx -t && service nginx restart
    ```

   
# 参考文章
1. [运维遇坑记录(3)-Nginx缓存了DNS解析造成后端不通](https://segmentfault.com/a/1190000022365954)
2. [nginx-upstream-dynamic-servers](https://github.com/GUI/nginx-upstream-dynamic-servers)
3. [详解Debian系统上如何编译安装Nginx](https://juejin.cn/post/7061998261904605214)
4. [Linux 如何查看修改DNS配置](https://www.cnblogs.com/kerrycode/p/5407635.html)