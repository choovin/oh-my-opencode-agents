# 快速开始

5分钟快速上手 Oh My OpenCode Agents。

## 安装完成后第一件事

### 1. 刷新用户组权限

```bash
# 将当前用户添加到docker组
newgrp docker

# 验证
docker ps
```

### 2. 检查所有服务状态

```bash
# 查看安装摘要
cat ~/ubuntu-setup-*.log | tail -50

# 检查各组件版本
docker --version
nvim --version
node --version
poetry --version
```

### 3. 访问OpenCode Manager

打开浏览器访问：
```
http://www.sailfish.com.cn
```

或者通过服务器IP：
```
http://<服务器IP>:5003
```

首次访问需要创建管理员账户。

### 4. 访问宝塔面板

```bash
# 查看宝塔访问信息
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

## 常用命令速查

### 开发工具

```bash
# Zoxide - 智能目录跳转
z documents    # 跳转到Documents目录
z nvim         # 跳转到nvim配置目录

# Lazygit - Git UI
lg             # 启动lazygit

# Lazydocker - Docker UI
ld             # 启动lazydocker

# Fzf - 模糊查找
Ctrl+R         # 搜索命令历史
```

### Python开发

```bash
# UV - 快速包管理
uv pip install requests
uv run python script.py

# Poetry - 项目管理
cd ~/projects
poetry new my-project
poetry add fastapi
poetry install
poetry shell
```

### Node.js开发

```bash
# 查看Node版本
node --version  # v20.x.x

# 使用pnpm（已安装）
pnpm install
pnpm dev
```

### Docker管理

```bash
# 查看容器
docker ps

# 使用Lazydocker
ld
```

### 服务器管理

```bash
# OpenCode Manager
sudo systemctl status opencode-manager
sudo systemctl restart opencode-manager
sudo journalctl -u opencode-manager -f

# Nginx
sudo /etc/init.d/nginx status
sudo /etc/init.d/nginx reload

# 宝塔面板
sudo bt start      # 启动
sudo bt stop       # 停止
sudo bt restart    # 重启
```

## 配置您的开发环境

### 配置Git

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main
```

### 配置Poetry

```bash
# 已在安装时配置，如需修改：
poetry config virtualenvs.in-project true
poetry config virtualenvs.prefer-active-python true
```

### 配置Zsh主题

编辑 `~/.zshrc`：

```bash
# 修改主题
ZSH_THEME="agnoster"  # 或 "powerlevel10k", "spaceship"
```

## 下一步

- [功能介绍](../features/overview.md) - 了解所有功能
- [配置指南](../configuration/environment.md) - 自定义配置
- [故障排除](../troubleshooting.md) - 解决问题
