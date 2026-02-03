# Nginx 集成指南

深入了解Nginx反向代理配置和OpenCode Manager集成。

## 🔄 反向代理架构

### 架构概述

```
用户访问 → Nginx (80端口) → OpenCode Manager (5003端口)
                     ↓
            宝塔面板管理
```

### 为什么使用反向代理？

1. **域名访问** - 通过www.sailfish.com.cn访问，而非端口号
2. **SSL终端** - 在Nginx层处理HTTPS
3. **负载均衡** - 未来可扩展到多个实例
4. **静态文件缓存** - Nginx高效处理静态资源
5. **WebSocket支持** - 完整支持实时通信

## 📁 配置文件详解

### 主配置文件位置

```
/www/server/panel/vhost/nginx/www.sailfish.com.cn.conf
```

### 配置内容解析

```nginx
server {
    listen 80;                          # 监听80端口
    listen [::]:80;                     # IPv6支持
    server_name www.sailfish.com.cn;     # 绑定的域名
    
    # 安全头部
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    
    # 日志配置
    access_log /www/wwwlogs/www.sailfish.com.cn.log;
    error_log /www/wwwlogs/www.sailfish.com.cn.error.log;
    
    # WebSocket支持
    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }
    
    # 静态文件缓存（可选fallback）
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        root /www/wwwroot/www.sailfish.com.cn/static;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # 反向代理到OpenCode Manager
    location / {
        proxy_pass http://127.0.0.1:5003;
        proxy_http_version 1.1;
        
        # 传递原始请求头
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket支持
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        
        # 超时设置
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        
        # 缓冲区优化
        proxy_buffering off;
        proxy_request_buffering off;
    }
}
```

## 🔧 配置管理

### 查看当前配置

```bash
# 查看主配置
cat /www/server/panel/vhost/nginx/www.sailfish.com.cn.conf

# 测试配置语法
sudo /www/server/nginx/sbin/nginx -t
```

### 修改配置

#### 通过宝塔面板（推荐）

1. 登录宝塔面板
2. 进入"网站"
3. 找到 www.sailfish.com.cn
4. 点击"设置" → "配置文件"
5. 修改后保存

#### 直接编辑配置文件

```bash
# 编辑配置
sudo nano /www/server/panel/vhost/nginx/www.sailfish.com.cn.conf

# 重载Nginx
sudo /etc/init.d/nginx reload
```

### 重载配置

```bash
# 方法1：使用系统脚本
sudo /etc/init.d/nginx reload

# 方法2：使用管理脚本
/opt/opencode-manager/reload-nginx.sh

# 方法3：使用API脚本
/opt/opencode-manager/nginx-api.sh reload
```

## 🌐 多域名配置

### 添加额外域名

编辑配置文件，修改 `server_name`：

```nginx
server {
    listen 80;
    server_name www.sailfish.com.cn sailfish.com.cn your-domain.com;
    # ...
}
```

### 为不同域名配置不同规则

```nginx
server {
    listen 80;
    server_name api.example.com;
    
    location / {
        proxy_pass http://127.0.0.1:3000;  # API服务
    }
}

server {
    listen 80;
    server_name www.example.com;
    
    location / {
        proxy_pass http://127.0.0.1:5003;  # OpenCode Manager
    }
}
```

## 🔒 SSL/HTTPS 配置

### 使用Let's Encrypt免费证书

#### 通过宝塔面板配置

1. 登录宝塔
2. 进入网站设置
3. 点击"SSL"
4. 选择"Let's Encrypt"
5. 申请证书
6. 启用"强制HTTPS"

配置会自动更新为：

```nginx
server {
    listen 80;
    server_name www.sailfish.com.cn;
    return 301 https://$server_name$request_uri;  # HTTP重定向到HTTPS
}

server {
    listen 443 ssl;
    server_name www.sailfish.com.cn;
    
    ssl_certificate /www/server/panel/vhost/cert/www.sailfish.com.cn/fullchain.pem;
    ssl_certificate_key /www/server/panel/vhost/cert/www.sailfish.com.cn/privkey.pem;
    
    # SSL优化
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
    ssl_prefer_server_ciphers off;
    
    # 其他配置...
}
```

### 手动配置SSL

```bash
# 编辑配置
sudo nano /www/server/panel/vhost/nginx/www.sailfish.com.cn.conf
```

添加SSL配置：
```nginx
server {
    listen 443 ssl;
    server_name www.sailfish.com.cn;
    
    ssl_certificate /path/to/your/certificate.crt;
    ssl_certificate_key /path/to/your/private.key;
    
    # 其他代理配置...
}

server {
    listen 80;
    server_name www.sailfish.com.cn;
    return 301 https://$server_name$request_uri;
}
```

## ⚡ 性能优化

### 启用Gzip压缩

```nginx
gzip on;
gzip_vary on;
gzip_min_length 1000;
gzip_proxied any;
gzip_comp_level 6;
gzip_types
    text/plain
    text/css
    text/xml
    application/json
    application/javascript
    application/rss+xml
    application/atom+xml
    image/svg+xml;
```

### 静态文件缓存

```nginx
location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
    root /www/wwwroot/www.sailfish.com.cn/static;
    expires 1y;
    add_header Cache-Control "public, immutable";
    access_log off;
}
```

### 缓冲区优化

```nginx
location / {
    proxy_pass http://127.0.0.1:5003;
    
    # 缓冲区
    proxy_buffer_size 4k;
    proxy_buffers 8 4k;
    proxy_busy_buffers_size 8k;
    
    # 禁用缓冲区（适合实时应用）
    proxy_buffering off;
    proxy_request_buffering off;
}
```

### 连接优化

```nginx
# /www/server/nginx/conf/nginx.conf

# 工作进程数（通常等于CPU核心数）
worker_processes auto;

# 连接数
events {
    worker_connections 4096;
    use epoll;
    multi_accept on;
}

# 文件描述符限制
worker_rlimit_nofile 65535;
```

## 🔍 故障排查

### 查看访问日志

```bash
# 实时查看访问日志
tail -f /www/wwwlogs/www.sailfish.com.cn.log

# 查看特定状态码
grep " 500 " /www/wwwlogs/www.sailfish.com.cn.log

# 查看IP访问频率
awk '{print $1}' /www/wwwlogs/www.sailfish.com.cn.log | sort | uniq -c | sort -rn | head -20
```

### 查看错误日志

```bash
# 实时查看错误日志
tail -f /www/wwwlogs/www.sailfish.com.cn.error.log

# 查看最近的错误
tail -100 /www/wwwlogs/www.sailfish.com.cn.error.log
```

### 常见错误

#### 502 Bad Gateway

原因：OpenCode Manager未运行或端口错误

解决：
```bash
# 检查服务状态
sudo systemctl status opencode-manager

# 重启服务
sudo systemctl restart opencode-manager

# 检查端口监听
sudo netstat -tlnp | grep 5003
```

#### 504 Gateway Timeout

原因：OpenCode Manager响应超时

解决：
```nginx
# 增加超时时间
location / {
    proxy_pass http://127.0.0.1:5003;
    proxy_connect_timeout 300s;
    proxy_send_timeout 300s;
    proxy_read_timeout 300s;
}
```

#### WebSocket连接失败

检查WebSocket配置：
```nginx
location / {
    proxy_pass http://127.0.0.1:5003;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

## 📝 最佳实践

### 1. 定期备份配置

```bash
# 备份脚本
#!/bin/bash
DATE=$(date +%Y%m%d)
cp /www/server/panel/vhost/nginx/www.sailfish.com.cn.conf \
   /backup/nginx-www.sailfish.com.cn.conf.$DATE

# 清理旧备份
find /backup -name "nginx-*.conf.*" -mtime +30 -delete
```

### 2. 配置版本控制

```bash
# 初始化Git仓库
cd /www/server/panel/vhost/nginx/
git init
git add .
git commit -m "Initial nginx config"

# 每次修改后提交
git add www.sailfish.com.cn.conf
git commit -m "Update SSL config"
```

### 3. 测试配置再重载

```bash
# 总是先测试
sudo /www/server/nginx/sbin/nginx -t

# 确认无误后再重载
sudo /etc/init.d/nginx reload
```

### 4. 监控关键指标

```bash
# 检查Nginx状态
curl -I http://www.sailfish.com.cn

# 检查响应时间
curl -o /dev/null -s -w "%{time_total}\n" http://www.sailfish.com.cn

# 并发测试（使用ab或wrk）
ab -n 1000 -c 100 http://www.sailfish.com.cn/
```

## 🔗 集成OpenCode Manager

### 配置软链接

```bash
# OpenCode Manager可以通过软链接读取Nginx配置
ls -la /opt/opencode-manager/nginx-configs

# 输出：
# lrwxrwxrwx 1 root root 40 /opt/opencode-manager/nginx-configs -> /www/server/panel/vhost/nginx/
```

### 使用管理API

```bash
# 查看所有Nginx配置
ls /opt/opencode-manager/nginx-configs/

# 查看特定配置
cat /opt/opencode-manager/nginx-configs/www.sailfish.com.cn.conf

# 重载Nginx
/opt/opencode-manager/reload-nginx.sh
```

### 自动化脚本

```bash
#!/bin/bash
# /usr/local/bin/sync-nginx-config.sh

# 从Git仓库更新配置
cd /opt/nginx-configs-git/
git pull

# 复制到Nginx目录
cp *.conf /www/server/panel/vhost/nginx/

# 测试配置
if /www/server/nginx/sbin/nginx -t; then
    # 重载Nginx
    /etc/init.d/nginx reload
    echo "Nginx config updated and reloaded"
else
    echo "Nginx config test failed, rollback required"
    exit 1
fi
```
