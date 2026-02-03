# 自定义配置

高级自定义选项和扩展。

## 🎨 自定义主题和外观

### Zsh 主题深度定制

#### 安装 Powerlevel10k

```bash
# 克隆主题
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

# 编辑 ~/.zshrc
ZSH_THEME="powerlevel10k/powerlevel10k"

# 重新加载
source ~/.zshrc

# 配置向导
p10k configure
```

#### 自定义提示符

编辑 `~/.p10k.zsh`：
```bash
# 显示元素
typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(
    os_icon                 # 操作系统图标
    dir                     # 当前目录
    vcs                     # Git状态
    prompt_char             # 提示字符
)

typeset -g POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(
    status                  # 最后命令状态
    command_execution_time  # 执行时间
    background_jobs         # 后台任务
    time                    # 时间
)

# 自定义颜色
typeset -g POWERLEVEL9K_DIR_BACKGROUND=4    # 蓝色背景
```

## 🔌 开发环境扩展

### 添加新的编程语言

#### 安装 Go

```bash
# 编辑安装脚本，添加install_go函数
install_go() {
    log_header "Go Installation"
    
    if ask_yn "Install Go (Golang)?" "y"; then
        log_info "Installing Go..."
        
        # 下载最新版本
        GO_VERSION=$(curl -s https://go.dev/VERSION?m=text | head -1)
        wget https://go.dev/dl/${GO_VERSION}.linux-amd64.tar.gz
        sudo tar -C /usr/local -xzf ${GO_VERSION}.linux-amd64.tar.gz
        
        # 添加到PATH
        echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.zshrc
        
        log_success "Go installed: $(go version)"
    fi
}
```

#### 安装 Rust

```bash
install_rust() {
    log_header "Rust Installation"
    
    if ask_yn "Install Rust (via rustup)?" "y"; then
        log_info "Installing Rust..."
        curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
        source $HOME/.cargo/env
        log_success "Rust installed: $(rustc --version)"
    fi
}
```

### 数据库工具

#### 安装 MySQL Client

```bash
install_mysql_client() {
    log_header "MySQL Client Installation"
    
    if ask_yn "Install MySQL client tools?" "y"; then
        sudo apt-get install -y mysql-client
        log_success "MySQL client installed"
    fi
}
```

#### 安装 PostgreSQL Client

```bash
install_postgres_client() {
    log_header "PostgreSQL Client Installation"
    
    if ask_yn "Install PostgreSQL client tools?" "y"; then
        sudo apt-get install -y postgresql-client
        log_success "PostgreSQL client installed"
    fi
}
```

### 云工具

#### 安装 AWS CLI

```bash
install_aws_cli() {
    log_header "AWS CLI Installation"
    
    if ask_yn "Install AWS CLI?" "y"; then
        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
        unzip awscliv2.zip
        sudo ./aws/install
        rm -rf aws awscliv2.zip
        log_success "AWS CLI installed: $(aws --version)"
    fi
}
```

#### 安装 Azure CLI

```bash
install_azure_cli() {
    log_header "Azure CLI Installation"
    
    if ask_yn "Install Azure CLI?" "y"; then
        curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
        log_success "Azure CLI installed: $(az --version)"
    fi
}
```

## 🐳 Docker 扩展

### 安装 Docker Compose

```bash
install_docker_compose() {
    log_header "Docker Compose Installation"
    
    if ask_yn "Install Docker Compose?" "y"; then
        # 安装插件版本
        sudo apt-get install -y docker-compose-plugin
        
        # 验证
        docker compose version
        log_success "Docker Compose installed"
    fi
}
```

### 配置 Docker Registry 镜像

```bash
configure_docker_registry() {
    log_header "Docker Registry Configuration"
    
    if ask_yn "Configure Docker registry mirrors for faster pulls in China?" "y"; then
        sudo mkdir -p /etc/docker
        sudo tee /etc/docker/daemon.json << 'EOF'
{
  "registry-mirrors": [
    "https://docker.mirrors.ustc.edu.cn",
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
EOF
        sudo systemctl restart docker
        log_success "Docker registry mirrors configured"
    fi
}
```

## 📊 监控工具

### 安装 Netdata

```bash
install_netdata() {
    log_header "Netdata Installation"
    
    if ask_yn "Install Netdata (system monitoring)?" "y"; then
        bash <(curl -Ss https://my-netdata.io/kickstart.sh)
        log_success "Netdata installed"
        log_info "Access at: http://$(hostname -I | awk '{print $1}'):19999"
    fi
}
```

### 安装 Prometheus + Grafana

```bash
install_monitoring_stack() {
    log_header "Monitoring Stack Installation"
    
    if ask_yn "Install Prometheus + Grafana monitoring stack?" "y"; then
        # 使用Docker部署
        mkdir -p ~/monitoring
        cd ~/monitoring
        
        # 创建docker-compose.yml
        cat > docker-compose.yml << 'EOF'
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
  
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
EOF
        
        docker compose up -d
        log_success "Monitoring stack installed"
        log_info "Prometheus: http://$(hostname -I | awk '{print $1}'):9090"
        log_info "Grafana: http://$(hostname -I | awk '{print $1}'):3000"
    fi
}
```

## 🔒 安全工具

### 安装 ClamAV 防病毒

```bash
install_clamav() {
    log_header "ClamAV Installation"
    
    if ask_yn "Install ClamAV antivirus?" "y"; then
        sudo apt-get install -y clamav clamav-daemon
        sudo freshclam  # 更新病毒库
        
        # 设置定时扫描
        sudo tee /etc/cron.daily/clamscan << 'EOF'
#!/bin/bash
clamscan -r /home --infected --remove=yes --log=/var/log/clamav/daily.log
EOF
        sudo chmod +x /etc/cron.daily/clamscan
        
        log_success "ClamAV installed and configured"
    fi
}
```

### 安装 Lynis 安全审计

```bash
install_lynis() {
    log_header "Lynis Security Audit"
    
    if ask_yn "Install Lynis (security auditing tool)?" "y"; then
        sudo apt-get install -y lynis
        log_success "Lynis installed"
        log_info "Run audit with: sudo lynis audit system"
    fi
}
```

## 📝 自定义日志

### 添加详细日志记录

```bash
# 在脚本开头添加
LOG_LEVEL=${LOG_LEVEL:-INFO}

# 增强日志函数
log_debug() {
    if [ "$LOG_LEVEL" = "DEBUG" ]; then
        echo -e "${BLUE}[DEBUG]${NC} $1" | tee -a "$LOG_FILE"
    fi
}

log_verbose() {
    echo -e "${CYAN}[VERBOSE]${NC} $1" | tee -a "$LOG_FILE"
}
```

### 安装日志分析工具

```bash
install_log_tools() {
    log_header "Log Analysis Tools"
    
    if ask_yn "Install log analysis tools (lnav, multitail)?" "y"; then
        sudo apt-get install -y lnav multitail
        log_success "Log tools installed"
    fi
}
```

## 🎨 创建自定义安装配置

### 场景1：前端开发工作站

```bash
#!/bin/bash
# frontend-setup.sh - 仅前端开发工具

components=(
    "install_system_updates"
    "install_git"
    "install_zsh"
    "install_nodejs"      # 包含npm/yarn/pnpm
    "install_docker"
    "install_btop"
    "install_tmux"
    "install_fzf"
)

for component in "${components[@]}"; do
    $component
    echo "Installed: $component"
done
```

### 场景2：Python数据科学环境

```bash
#!/bin/bash
# datascience-setup.sh

components=(
    "install_system_updates"
    "install_git"
    "install_zsh"
    "install_python_tools"  # UV, Poetry
    "install_docker"
    "install_jupyter"       # 可添加
    "install_conda"         # 可添加
    "install_neovim"
)
```

### 场景3：微服务生产环境

```bash
#!/bin/bash
# microservices-setup.sh

components=(
    "install_system_updates"
    "install_git"
    "install_docker"
    "install_kubectl"       # 可添加
    "install_helm"          # 可添加
    "install_nginx"
    "install_monitoring"    # Prometheus, Grafana
    "install_baota_panel"
)
```

## 🔧 自动化扩展脚本

### 安装后自动配置

```bash
#!/bin/bash
# post-install-setup.sh

# 配置Git
setup_git() {
    if [ -z "$(git config --global user.name)" ]; then
        read -p "Enter Git username: " git_name
        git config --global user.name "$git_name"
    fi
    
    if [ -z "$(git config --global user.email)" ]; then
        read -p "Enter Git email: " git_email
        git config --global user.email "$git_email"
    fi
    
    git config --global init.defaultBranch main
    git config --global core.editor nvim
}

# 配置SSH密钥
setup_ssh() {
    if [ ! -f ~/.ssh/id_ed25519 ]; then
        ssh-keygen -t ed25519 -C "$(git config --global user.email)"
        eval "$(ssh-agent -s)"
        ssh-add ~/.ssh/id_ed25519
        echo "SSH public key:"
        cat ~/.ssh/id_ed25519.pub
    fi
}

# 配置Docker
setup_docker() {
    # 登录Docker Hub（如果需要）
    if ! docker info 2>/dev/null | grep -q "Username"; then
        docker login
    fi
}

# 运行所有设置
setup_git
setup_ssh
setup_docker
```

## 📋 配置文件模板

### 创建配置文件模板

```bash
# ~/.oh-my-opencode-agents.conf

# 安装选项
INSTALL_ZSH=true
INSTALL_NEOVIM=true
INSTALL_DOCKER=true
INSTALL_NODEJS=true
INSTALL_PYTHON_TOOLS=true
INSTALL_BAOTA=true
INSTALL_NGINX=true
INSTALL_OPENCODE_MANAGER=true

# 自定义选项
ZSH_THEME="robbyrussell"
NODEJS_VERSION="20"
DOCKER_REGISTRY_MIRRORS="true"
BAOTA_SSL=true
NGINX_SSL=true
CUSTOM_DOMAIN="your-domain.com"

# 高级选项
AUTO_START_SERVICES=true
ENABLE_BACKUP=true
BACKUP_RETENTION_DAYS=30
```

### 读取配置文件

```bash
#!/bin/bash
# 在脚本开头读取配置

CONFIG_FILE="$HOME/.oh-my-opencode-agents.conf"

if [ -f "$CONFIG_FILE" ]; then
    source "$CONFIG_FILE"
    log_info "Loaded configuration from $CONFIG_FILE"
else
    log_warn "No configuration file found, using defaults"
fi

# 使用配置变量
if [ "${INSTALL_ZSH:-true}" = "true" ]; then
    install_zsh
fi
```
