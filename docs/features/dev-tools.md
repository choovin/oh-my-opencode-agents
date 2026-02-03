# 开发工具详解

深入了解 Oh My OpenCode Agents 集成的每个开发工具。

## 📝 Neovim (v0.10+)

### 特性
- 现代Lua配置架构
- 内置LSP (Language Server Protocol)
- Tree-sitter语法高亮
- 丰富的插件生态

### 配置
安装时自动克隆的Neovim配置：
```
~/.config/nvim/
```

### 常用命令
```bash
nvim filename      # 打开文件
nvim .             # 打开当前目录
# 或在Zsh中使用别名
vim filename       # 等同于 nvim
vi filename        # 等同于 nvim
```

### 学习资源
- [Neovim官方文档](https://neovim.io/doc/)
- [Learn Vimscript the Hard Way](http://learnvimscriptthehardway.stevelosh.com/)

## 🐚 Zsh + Oh-My-Zsh

### 配置位置
```
~/.zshrc
```

### 启用的插件
- **git** - Git命令别名和自动补全
- **docker** - Docker命令补全
- **zoxide** - z命令智能跳转
- **fzf** - 模糊查找集成
- **zsh-history-substring-search** - 前缀历史搜索

### 快捷键
- `Ctrl+R` - 模糊搜索命令历史
- `↑/↓` - 前缀历史搜索（输入前缀后按方向键）
- `z <目录名>` - 智能跳转到常用目录

### 自定义主题
编辑 `~/.zshrc`：
```bash
ZSH_THEME="agnoster"  # 或其他主题
```

## 🐋 Docker & Lazydocker

### Docker 配置
- 当前用户已添加到docker组
- 需要重新登录或运行 `newgrp docker` 生效

### Lazydocker 使用
```bash
ld    # 启动 Lazydocker TUI
```

功能：
- 查看容器、镜像、卷
- 实时日志监控
- 资源使用统计
- 快速操作容器（启动、停止、删除）

## 🐍 Python 工具链

### UV - 极速包管理
```bash
# 安装包（比pip快10-100倍）
uv pip install requests

# 运行脚本
uv run python script.py

# 创建虚拟环境
uv venv .venv
```

### Poetry - 项目管理
```bash
# 创建新项目
poetry new my-project

# 添加依赖
poetry add fastapi
poetry add --group dev pytest

# 安装所有依赖
poetry install

# 进入虚拟环境
poetry shell

# 运行命令
poetry run python manage.py runserver
```

**配置**：
- 虚拟环境存储在项目目录 (`virtualenvs.in-project = true`)
- 自动生成 `poetry.lock` 锁定依赖版本

## 📦 Node.js 生态

### Node.js LTS (v20.x)
已安装：
- Node.js v20.x
- npm (内置)
- yarn (全局安装)
- pnpm (全局安装)

### 版本管理
```bash
node --version   # v20.x.x
npm --version
yarn --version
pnpm --version
```

### 常用命令
```bash
# npm
npm install
npm run dev

# yarn
yarn install
yarn dev

# pnpm（推荐，速度快）
pnpm install
pnpm dev
```

## 🔍 搜索工具

### Ripgrep (rg)
```bash
# 快速代码搜索
rg "function main" src/
rg -i "todo" --type js    # 不区分大小写，仅限js文件
rg -C 3 "pattern"         # 显示上下文3行
```

### Fd
```bash
# 快速文件查找
fd "\.py$"              # 查找所有Python文件
fd -e js -e ts          # 查找js或ts文件
fd --hidden config      # 包含隐藏文件
```

### Fzf
```bash
# 交互式模糊查找
Ctrl+R                  # 搜索命令历史
fzf                     # 查找文件
ls | fzf                # 管道中使用
```

## 🖥️ 系统监控

### Btop
```bash
btop    # 启动系统监控
```

显示：
- CPU、内存、磁盘、网络实时使用
- 进程列表
- 美观的图形界面

### Tmux
```bash
tmux new -s session_name    # 创建新会话
tmux attach -t session_name # 附加到会话
tmux ls                     # 列出会话
```

常用快捷键：
- `Ctrl+B, C` - 创建新窗口
- `Ctrl+B, N` - 下一个窗口
- `Ctrl+B, D` - 分离会话
- `Ctrl+B, %` - 垂直分割
- `Ctrl+B, "` - 水平分割

## 🚀 Zoxide - 智能目录跳转

### 使用方法
```bash
z documents      # 跳转到 ~/Documents
z proj           # 跳转到 ~/projects/myproject
z nvim           # 跳转到 ~/.config/nvim
z -              # 返回上一个目录
```

### 原理
Zoxide 会学习您的导航习惯，根据访问频率智能匹配目录。

## 📚 学习资源

### 官方文档
- [Neovim](https://neovim.io/doc/)
- [Oh-My-Zsh](https://ohmyz.sh/)
- [Docker](https://docs.docker.com/)
- [Poetry](https://python-poetry.org/docs/)
- [UV](https://github.com/astral-sh/uv)

### 快捷键速查
- [Zsh快捷键](https://github.com/ohmyzsh/ohmyzsh/wiki/Cheatsheet)
- [Vim快捷键](https://vim.rtorr.com/)
- [Tmux快捷键](https://tmuxcheatsheet.com/)
