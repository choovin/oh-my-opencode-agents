# 服务器管理指南

宝塔面板和服务器管理详解。

## 🎛️ 宝塔面板 (Baota Panel)

### 访问面板

```bash
# 查看访问信息
sudo bt default
```

输出示例：
```
宝塔面板默认信息：
外网面板地址: http://123.45.67.89:8888/abc123def
内网面板地址: http://192.168.1.100:8888/abc123def
username: admin
password: your_secure_password
```

### 常用宝塔命令

```bash
sudo bt                    # 显示菜单
sudo bt start             # 启动面板
sudo bt stop              # 停止面板
sudo bt restart           # 重启面板
sudo bt reload            # 重载面板
sudo bt default           # 查看面板信息
```

### 网站管理

#### 查看已安装网站
```bash
ls -la /www/wwwroot/
```

#### 网站目录结构
```
/www/wwwroot/
├── www.sailfish.com.cn/          # OpenCode Manager 网站
│   ├── index.html               # 默认页面
│   └── static/                  # 静态文件
```

#### 添加新网站
1. 登录宝塔面板
2. 点击"网站" → "添加站点"
3. 输入域名和根目录
4. 选择PHP版本（如需）
5. 点击提交

### SSL证书配置

#### 使用Let's Encrypt免费证书
1. 进入网站设置
2. 点击"SSL"
3. 选择"Let's Encrypt"
4. 勾选同意服务条款
5. 点击"申请"
6. 启用"强制HTTPS"

#### 手动上传证书
1. 获取证书文件（.crt/.key）
2. 在SSL页面选择"其他证书"
3. 粘贴证书内容
4. 保存并启用

### 数据库管理

#### 安装数据库
宝塔支持一键安装：
- MySQL 5.5/5.6/5.7/8.0
- MariaDB
- PostgreSQL
- MongoDB
- Redis

#### 使用命令行
```bash
# MySQL
mysql -u root -p

# Redis
redis-cli
```

### 文件管理

宝塔提供Web文件管理器：
- 上传/下载文件
- 在线编辑代码
- 压缩/解压
- 权限管理

### 计划任务 (Cron)

1. 登录宝塔
2. 点击"计划任务"
3. 添加新任务：
   - 任务类型：Shell脚本
   - 执行周期：每天/每周/自定义
   - 脚本内容：命令或脚本路径

### 监控功能

宝塔内置监控：
- CPU使用率
- 内存使用
- 磁盘I/O
- 网络流量
- 负载均衡

## 🌐 Nginx 服务器

### Nginx 配置结构

```
/www/server/
├── nginx/                       # Nginx主目录
│   ├── sbin/nginx              # 可执行文件
│   ├── conf/nginx.conf         # 主配置文件
│   └── logs/                   # 日志目录
├── panel/vhost/nginx/          # 虚拟主机配置
│   └── www.sailfish.com.cn.conf
└── wwwlogs/                    # 网站访问日志
```

### 主要配置文件

#### 虚拟主机配置
```bash
cat /www/server/panel/vhost/nginx/www.sailfish.com.cn.conf
```

关键配置：
```nginx
server {
    listen 80;
    server_name www.sailfish.com.cn;
    
    # 反向代理到OpenCode Manager
    location / {
        proxy_pass http://127.0.0.1:5003;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        
        # WebSocket支持
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
}
```

### Nginx 管理命令

```bash
# 启动/停止/重载
sudo /etc/init.d/nginx start
sudo /etc/init.d/nginx stop
sudo /etc/init.d/nginx restart
sudo /etc/init.d/nginx reload
sudo /etc/init.d/nginx status

# 测试配置
sudo /www/server/nginx/sbin/nginx -t

# 查看版本
sudo /www/server/nginx/sbin/nginx -v
```

### 日志管理

#### 访问日志
```bash
# 实时查看
tail -f /www/wwwlogs/www.sailfish.com.cn.log

# 分析日志
sudo /www/server/nginx/sbin/nginx -s reopen  # 重新打开日志文件
```

#### 错误日志
```bash
tail -f /www/wwwlogs/www.sailfish.com.cn.error.log
```

### 性能优化

#### 启用Gzip压缩
```nginx
gzip on;
gzip_vary on;
gzip_min_length 1000;
gzip_types text/plain text/css application/json application/javascript;
```

#### 静态文件缓存
```nginx
location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
    expires 30d;
    add_header Cache-Control "public, immutable";
}
```

## 🔗 OpenCode Manager 集成

### Nginx 配置软链接

```bash
# 查看软链接
ls -la /opt/opencode-manager/nginx-configs

# 输出：
# /opt/opencode-manager/nginx-configs -> /www/server/panel/vhost/nginx/
```

### 管理脚本

#### Nginx API脚本
```bash
# 查看Nginx状态
/opt/opencode-manager/nginx-api.sh status

# 启动/停止/重载
/opt/opencode-manager/nginx-api.sh start
/opt/opencode-manager/nginx-api.sh stop
/opt/opencode-manager/nginx-api.sh reload

# 测试配置
/opt/opencode-manager/nginx-api.sh test
```

#### 快速重载脚本
```bash
# 重载Nginx配置
/opt/opencode-manager/reload-nginx.sh
```

## 🛡️ 安全配置

### 防火墙设置

#### UFW (Ubuntu Firewall)
```bash
# 安装
sudo apt install ufw

# 配置规则
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp      # SSH
sudo ufw allow 80/tcp      # HTTP
sudo ufw allow 443/tcp     # HTTPS
sudo ufw allow 8888/tcp    # 宝塔面板

# 启用
sudo ufw enable
sudo ufw status
```

### 宝塔安全入口

宝塔的安全入口URL格式：
```
http://<IP>:8888/<随机字符>
```

建议：
1. 修改默认端口
2. 绑定域名访问
3. 启用SSL
4. 设置IP白名单

### Fail2ban（可选）

```bash
# 安装
sudo apt install fail2ban

# 配置（防止暴力破解）
sudo tee /etc/fail2ban/jail.local << EOF
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 5

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
EOF

# 启动
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

## 📊 监控与维护

### 系统监控

#### 使用Btop
```bash
btop
```

#### 使用宝塔监控
宝塔面板 → 监控 → 查看图表

### 日志轮转

宝塔自动配置日志轮转：
```bash
# 查看配置
ls /www/server/panel/vhost/nginx/*.conf | xargs grep access_log

# 手动轮转
sudo /www/server/nginx/sbin/nginx -s reopen
```

### 备份策略

#### 自动备份脚本
```bash
#!/bin/bash
# 添加到crontab每天执行

# 备份宝塔配置
tar -czf /backup/bt-$(date +%Y%m%d).tar.gz /www/server/panel/

# 备份网站数据
tar -czf /backup/www-$(date +%Y%m%d).tar.gz /www/wwwroot/

# 备份Nginx配置
tar -czf /backup/nginx-$(date +%Y%m%d).tar.gz /www/server/panel/vhost/nginx/

# 清理旧备份（保留7天）
find /backup/ -name "*.tar.gz" -mtime +7 -delete
```
