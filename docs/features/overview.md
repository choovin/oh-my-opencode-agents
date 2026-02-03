# 功能概览

Oh My OpenCode Agents 提供完整的开发环境和服务器管理解决方案。

## 🛠️ 开发工具链

### 版本控制
- **Git** - 分布式版本控制系统
- **Lazygit** - 终端Git UI，快捷键操作高效便捷

### 编辑器与IDE
- **Neovim** (v0.10+) - 现代Vim编辑器，支持Lua配置
  - 自动安装自定义配置
  - 支持LSP、代码补全、语法高亮
  - 插件管理器集成

### Shell环境
- **Zsh + Oh-My-Zsh** - 强大的Shell框架
  - 主题：robbyrussell
  - 插件：git, docker, zoxide, fzf, history-substring-search
  - 别名：vim=nvim, lg=lazygit, ld=lazydocker

### 容器化
- **Docker CE** - 最新的官方Docker
- **Lazydocker** - Docker容器管理UI

### Python生态
- **UV** - 极速Python包管理器（Rust编写）
- **Poetry** - 现代Python依赖管理
  - 自动配置 virtualenvs.in-project=true
  - 支持 pyproject.toml

### Node.js生态
- **Node.js LTS** (v20.x) - 官方NodeSource仓库
- **npm, yarn, pnpm** - 多包管理器支持

### 开发实用工具
- **Zoxide** - 智能目录跳转（z命令）
- **Fzf** - 模糊查找（Ctrl+R搜索历史）
- **Tmux** - 终端复用器
- **Btop** - 现代化系统监控
- **Ripgrep (rg)** - 快速代码搜索
- **Fd** - 快速文件查找

## 🌐 服务器管理套件

### OpenCode Manager
完整的开发环境管理平台：

- **Git仓库管理** - 克隆、管理多个仓库
- **文件浏览器** - 树形目录视图，支持拖拽上传
- **实时聊天** - 与OpenCode Agent对话
- **WebSocket支持** - 实时消息推送
- **任务管理** - 查看和控制Agent任务
- **Git工作流** - 支持多分支同时工作

**访问方式：**
- 域名：http://www.sailfish.com.cn
- 直接：http://<服务器IP>:5003

### 宝塔面板
专业的Web服务器管理面板：

- **网站管理** - 轻松添加和管理虚拟主机
- **SSL证书** - 一键申请Let's Encrypt证书
- **数据库** - MySQL、PostgreSQL、Redis管理
- **文件管理** - Web文件浏览器和编辑器
- **监控** - 系统资源监控图表
- **防火墙** - 可视化防火墙规则

**特点：**
- 安装后运行 `sudo bt default` 查看访问地址
- 支持中文界面
- 丰富的插件生态

### Nginx 反向代理
高性能Web服务器配置：

- **预配置网站** - www.sailfish.com.cn
- **反向代理** - 自动代理到OpenCode Manager (5003端口)
- **WebSocket支持** - 完整代理WebSocket连接
- **静态文件缓存** - 自动缓存优化
- **Gzip压缩** - 传输压缩支持
- **安全头部** - X-Frame-Options, X-XSS-Protection等

**配置文件位置：**
```
/www/server/panel/vhost/nginx/www.sailfish.com.cn.conf
```

## 🔧 系统集成特性

### 自动配置同步

OpenCode Manager 可以通过软链接管理Nginx配置：

```bash
# Nginx配置软链接
/opt/opencode-manager/nginx-configs -> /www/server/panel/vhost/nginx

# 管理脚本
/opt/opencode-manager/nginx-api.sh      # 状态/启动/停止/重载
/opt/opencode-manager/reload-nginx.sh    # 快速重载
```

### 域名集成

预配置的域名解析：

- **本地测试** - 自动添加到 /etc/hosts
- **外部访问** - 配置DNS解析到服务器IP
- **SSL支持** - 可通过宝塔面板一键配置

### 服务管理

所有服务都配置了systemd管理：

```bash
# OpenCode Manager
sudo systemctl start opencode-manager
sudo systemctl stop opencode-manager
sudo systemctl restart opencode-manager
sudo journalctl -u opencode-manager -f

# Nginx
sudo /etc/init.d/nginx start
sudo /etc/init.d/nginx stop
sudo /etc/init.d/nginx reload

# 宝塔面板
sudo bt start
sudo bt stop
sudo bt restart
```

## 📊 监控与日志

### 安装日志
所有操作详细记录：

```bash
# 查看安装日志
cat ~/ubuntu-setup-*.log

# 实时查看
tail -f ~/ubuntu-setup-*.log
```

### 服务日志

```bash
# OpenCode Manager
sudo journalctl -u opencode-manager -f

# Nginx
sudo tail -f /www/wwwlogs/www.sailfish.com.cn.log

# 宝塔
sudo tail -f /www/server/panel/logs/*.log
```

### 备份机制

自动备份配置变更：

```bash
# 备份目录
~/.config-backups/

# 查看备份
ls -la ~/.config-backups/
```

## 🎯 使用场景

### 个人开发服务器
- 完整的开发环境一键部署
- 代码托管和版本控制
- Docker容器化开发

### 团队协作平台
- OpenCode Manager多用户支持
- Git仓库集中管理
- 统一的开发环境配置

### 生产环境部署
- Nginx高性能反向代理
- SSL证书自动管理
- 系统监控和告警

### 学习和实验
- 最新开发工具快速体验
- 容器化技术实践
- 服务器管理学习

## 📚 下一步

- [开发工具详解](dev-tools.md) - 深入了解每个工具
- [服务器管理](server-management.md) - 宝塔和Nginx高级用法
- [Nginx集成](nginx-integration.md) - 反向代理配置指南
