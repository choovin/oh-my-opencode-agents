# 首次运行配置

安装完成后的初始化配置指南。

## 1. OpenCode Manager 首次配置

### 创建管理员账户

首次访问 `http://www.sailfish.com.cn` 时，系统会提示创建管理员账户：

1. 输入管理员邮箱
2. 设置安全密码（建议使用强密码）
3. 确认密码
4. 点击"Create Account"

### 配置OpenCode CLI（可选）

在服务器上配置OpenCode CLI：

```bash
# 安装OpenCode TUI
npm install -g @opencode/tui

# 配置OpenCode连接到Manager
opencode config set api.url http://localhost:5003
opencode config set api.token <your-token>
```

## 2. 宝塔面板初始化

### 安全设置

1. 首次登录后，立即修改默认密码
2. 在安全设置中启用二次验证（推荐）
3. 配置防火墙规则

### 修改默认端口（推荐）

```bash
# 修改宝塔面板端口为随机端口
sudo bt 8
# 输入新端口号（如 12345）
```

### 绑定域名

1. 登录宝塔面板
2. 进入"网站"菜单
3. 找到 www.sailfish.com.cn
4. 点击"设置" → "域名管理"
5. 添加您的真实域名（如果有）

## 3. Nginx配置优化

### 查看当前配置

```bash
# 宝塔nginx配置文件
cat /www/server/panel/vhost/nginx/www.sailfish.com.cn.conf

# 主nginx配置
ls -la /www/server/panel/vhost/nginx/
```

### 添加SSL证书（生产环境推荐）

1. 登录宝塔面板
2. 进入"网站" → 找到 www.sailfish.com.cn
3. 点击"设置" → "SSL"
4. 选择"Let's Encrypt"免费证书
5. 勾选"强制HTTPS"

### 配置自定义域名

如果您有自己的域名：

1. 将域名DNS解析到服务器IP
2. 在宝塔中添加您的域名
3. 更新nginx配置中的server_name

```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    # ...
}
```

## 4. 安全配置建议

### 防火墙配置

```bash
# 使用ufw配置防火墙
sudo ufw allow 22/tcp      # SSH
sudo ufw allow 80/tcp      # HTTP
sudo ufw allow 443/tcp     # HTTPS
sudo ufw allow 8888/tcp    # 宝塔面板（如果使用默认端口）
sudo ufw enable
```

### 修改SSH端口（可选）

```bash
# 编辑SSH配置
sudo nano /etc/ssh/sshd_config

# 修改端口（如改为2222）
Port 2222

# 重启SSH
sudo systemctl restart sshd
```

### 禁用root登录（推荐）

```bash
# 创建普通用户
sudo adduser developer
sudo usermod -aG sudo developer

# 编辑SSH配置禁用root登录
sudo nano /etc/ssh/sshd_config

# 设置：
PermitRootLogin no
PasswordAuthentication no  # 如果使用密钥认证
```

## 5. 备份配置

### 自动备份脚本

创建备份脚本：

```bash
sudo tee /usr/local/bin/backup-config.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/backup/$(date +%Y%m%d-%H%M%S)"
mkdir -p $BACKUP_DIR

# 备份宝塔配置
cp -r /www/server/panel $BACKUP_DIR/

# 备份nginx配置
cp -r /www/server/panel/vhost/nginx $BACKUP_DIR/

# 备份网站数据
cp -r /www/wwwroot $BACKUP_DIR/

# 压缩备份
tar -czf $BACKUP_DIR.tar.gz $BACKUP_DIR
rm -rf $BACKUP_DIR

echo "Backup completed: $BACKUP_DIR.tar.gz"
EOF

sudo chmod +x /usr/local/bin/backup-config.sh
```

### 设置定时备份

```bash
# 每天凌晨2点备份
sudo crontab -e

# 添加：
0 2 * * * /usr/local/bin/backup-config.sh
```

## 6. 监控设置

### 使用宝塔监控

宝塔面板内置了服务器监控功能：

1. 登录宝塔面板
2. 点击左侧"监控"菜单
3. 查看CPU、内存、磁盘、网络使用情况

### 安装Netdata（可选）

```bash
# 使用宝塔安装Netdata
sudo bt install netdata

# 访问
http://<服务器IP>:19999
```

## 7. 下一步

- [功能介绍](../features/overview.md) - 探索所有功能
- [配置指南](../configuration/environment.md) - 高级配置
- [故障排除](../troubleshooting.md) - 解决问题
