# Ubuntu Server Initial Setup Script

[![Ubuntu](https://img.shields.io/badge/Ubuntu-20.04%2F22.04%2F24.04-orange.svg)](https://ubuntu.com)
[![Bash](https://img.shields.io/badge/Bash-4.0%2B-green.svg)](https://www.gnu.org/software/bash/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

[English](README.md) | [简体中文](README.zh-CN.md)

A comprehensive, interactive setup script for Ubuntu servers with essential modern development tools.

---

## 🚀 Quick Start

### First Time Commands

```bash
# Clone the repository
git clone https://github.com/typhoon1217/ubuntu-server-init.git
cd ubuntu-server-init

# Make executable
chmod +x ubuntu-server-setup.sh

# Interactive mode (recommended for first use)
./ubuntu-server-setup.sh

# Full auto mode (for CI/CD or automation)
./ubuntu-server-setup.sh -y
```

---

## ✨ Features

- 🎯 **Interactive Installation** - Y/N prompts for each component
- ⚡ **Auto Mode** - `-y` flag for fully automated installation
- 🔍 **Before/After Status** - Shows installed vs newly installed
- 🎨 **Colorized Output** - Clear, readable feedback with severity levels
- 🔒 **Robust Error Handling** - Strict error checking with detailed logging
- 💾 **Automatic Backups** - Saves existing configurations before modification
- 📊 **Comprehensive Logging** - Full logs saved with timestamps
- 🐳 **Docker User Setup** - Automatic user group configuration
- 🐚 **Smart Shell Config** - Zsh with prefix-based history search
- ⚡ **Non-Interactive Mode** - Use `-y` flag for fully automated installation

---

## 📦 What Gets Installed

### Core Tools

| Tool | Version | Purpose |
|------|---------|---------|
| **Git** | Latest | Version control system |
| **Zsh + Oh-My-Zsh** | Latest | Enhanced shell with framework |
| **Zoxide** | Latest | Smart cd command (remembers directories) |
| **Lazygit** | Latest | Terminal UI for git |
| **Lazydocker** | Latest | Terminal UI for Docker |
| **Docker CE** | Latest | Containerization platform |
| **Neovim** | v0.10+ | Modern text editor |
| **LuaRocks** | Latest | Lua package manager |
| **Node.js LTS** | v20.x+ | JavaScript runtime (npm, yarn, pnpm) |
| **UV** | Latest | Fast Python package manager |
| **GCC & Build Tools** | Latest | Compiler and development essentials |

### Additional Tools

| Tool | Purpose |
|------|---------|
| **btop** | Modern system resource monitor |
| **tmux** | Terminal multiplexer |
| **fzf** | Fuzzy finder for files and history |
| **ripgrep (rg)** | Fast grep alternative |
| **fd** | Fast find alternative |

---

## 🎮 Usage

### Interactive Mode (Recommended for First Use)

```bash
./ubuntu-server-setup.sh
```

The script will:
1. Show pre-installation status (what's already installed)
2. Check prerequisites (sudo access, internet connection)
3. Present Y/N prompts for each component
4. Install selected components with progress feedback
5. Configure post-installation settings
6. Display detailed before/after summary

### Non-Interactive Mode (Auto-Yes)

```bash
./ubuntu-server-setup.sh -y
# or
./ubuntu-server-setup.sh --yes
```

Perfect for:
- 🤖 CI/CD automated deployments
- 🔄 Batch server configuration
- ⏱️ Unattended installations

### Display Help

```bash
./ubuntu-server-setup.sh --help
```

---

## 📋 Command Examples

### First Run Example

```bash
# 1. Download script
curl -O https://raw.githubusercontent.com/typhoon1217/ubuntu-server-init/main/ubuntu-server-setup.sh

# 2. Make executable
chmod +x ubuntu-server-setup.sh

# 3. Run (interactive mode)
./ubuntu-server-setup.sh

# 4. Check logs
cat ~/ubuntu-setup-*.log
```

### Automation Example

```bash
# Full automatic installation
./ubuntu-server-setup.sh -y

# Run as root (not recommended)
sudo bash ubuntu-server-setup.sh -y
```

---

## 🎯 Expected Output

### Interactive Mode Output Example

```
========================================
Pre-Installation Status Check
========================================

[INFO] Checking what is currently installed...

  ✓ git: git version 2.43.0
  ✗ zsh: not installed
  ✗ lazygit: not installed
  ✓ docker: Docker version 24.0.7
  ...

Continue with installation? [Y/n]: y

========================================
Git Installation
========================================
[WARN] Git is already installed (git version 2.43.0)
Reinstall/upgrade git? [y/N]: n

...

========================================
Installation Summary
========================================

Newly Installed:
  ✓ zsh: zsh 5.9
  ✓ lazygit: version=0.40.2
  ✓ neovim: NVIM v0.9.5

Upgraded:
  ↑ docker: Docker version 24.0.7 → Docker version 25.0.0

Already Installed (unchanged):
  • git: git version 2.43.0
  • tmux: tmux 3.3a

Actions performed during this run:
  ✓ System Updates
  ✓ Zsh + Oh-My-Zsh
  ✓ Lazygit
  ✓ Docker CE
  ✓ Neovim
  ✓ Zsh Config
```

### Auto-Yes Mode Output

```bash
./ubuntu-server-setup.sh -y

# All prompts show [AUTO-YES]:
Continue with installation? [AUTO-YES]
Install/upgrade git? [AUTO-YES]
# ... everything installs automatically
```

---

## 🔧 Post-Installation

### Important Steps

1. **Log out and back in** (required for Docker group and shell changes)
   ```bash
   # Or refresh Docker group immediately:
   newgrp docker
   ```

2. **Test installations:**
   ```bash
   docker --version
   docker run hello-world
   nvim --version
   lazygit --version
   lazydocker --version
   ```

3. **Check your shell:**
   ```bash
   echo $SHELL
   # Should show: /usr/bin/zsh
   ```

### Zsh Features

The configured zsh includes:

- **Prefix-based history search**: Type `do` and press ↑ to see only commands starting with `do`
- **Oh-my-zsh plugins**: git, docker, zoxide, fzf, history-substring-search
- **Smart directory navigation**: Use `z <partial-name>` to jump to frequently used directories
- **Fuzzy search**: Press `Ctrl+R` for fuzzy command history search

### Aliases

The following aliases are configured in `.zshrc`:

```bash
vim='nvim'       # Use Neovim instead of Vim
vi='nvim'        # Use Neovim instead of Vi
lg='lazygit'     # Quick access to lazygit
ld='lazydocker'  # Quick access to lazydocker
```

---

## 📁 Configuration Files

| Type | Location |
|------|----------|
| Log file | `~/ubuntu-setup-YYYYMMDD-HHMMSS.log` |
| Backups | `~/.config-backups/YYYYMMDD-HHMMSS/` |
| Zsh config | `~/.zshrc` |
| Neovim config | `~/.config/nvim/` |

### Neovim Configuration

The script clones your custom Neovim configuration from:
- Primary: `git@github.com:typhoon1217/nvimconfig.git` (SSH)
- Fallback: `https://github.com/typhoon1217/nvimconfig.git` (HTTPS)

**Note**: SSH requires GitHub SSH key setup. If SSH fails, HTTPS is attempted automatically.

---

## 🛡️ Security Considerations

### Docker Group

⚠️ **Important**: Members of the `docker` group have root-level privileges on the host system.

- Only add trusted users to the docker group
- Consider using Docker rootless mode for production environments
- Read more: https://docs.docker.com/engine/security/rootless/

### Backups

All existing configurations are backed up to `~/.config-backups/` before modification:

```bash
# View backups
ls -la ~/.config-backups/

# Restore a backup
cp -r ~/.config-backups/20250122-143022/nvim-143500 ~/.config/nvim
```

---

## 🐛 Troubleshooting

### Common Issues

**Issue**: Script fails with "permission denied"
```bash
# Solution: Make script executable
chmod +x ubuntu-server-setup.sh
```

**Issue**: Docker commands require sudo
```bash
# Solution: Log out and back in, or run:
newgrp docker
```

**Issue**: Neovim config clone fails
```bash
# Solution: Setup SSH key for GitHub
ssh-keygen -t ed25519 -C "your_email@example.com"
cat ~/.ssh/id_ed25519.pub
# Add the public key to GitHub: Settings → SSH keys
```

**Issue**: Zsh not activated
```bash
# Solution: Check default shell
echo $SHELL

# If not zsh, change manually:
chsh -s $(which zsh)
# Then log out and back in
```

**Issue**: History search not working with arrows
```bash
# Solution: Check plugin installation
ls ~/.oh-my-zsh/custom/plugins/zsh-history-substring-search

# If missing, clone manually:
git clone https://github.com/zsh-users/zsh-history-substring-search \
  ~/.oh-my-zsh/custom/plugins/zsh-history-substring-search
```

### Check Installation

```bash
# View detailed log
cat ~/ubuntu-setup-*.log

# Check specific tool
which lazygit
lazygit --version

# Verify Docker group
groups
# Should include 'docker'

# Test Docker without sudo
docker ps
```

---

## ⚙️ Customization

### Modify Components

Edit the script to skip or add components:

```bash
# Comment out unwanted installations in main() function
# install_btop && installed_components+=("Btop")  # Disabled
```

### Change Zsh Theme

Edit `~/.zshrc`:

```bash
# Change from robbyrussell to another theme
ZSH_THEME="agnoster"  # or "powerlevel10k", "spaceship", etc.
```

### Add More Plugins

Edit `~/.zshrc`:

```bash
plugins=(
    git
    docker
    zoxide
    fzf
    zsh-history-substring-search
    # Add more plugins here
    kubectl
    terraform
    aws
)
```

---

## 📋 Requirements

- Ubuntu 20.04, 22.04, or 24.04
- Sudo privileges
- Internet connection
- ~500MB disk space for all tools

---

## ✨ Best Practices Applied

This script follows industry best practices:

✅ Strict error handling (`set -euo pipefail`)
✅ Color codes only for terminal output
✅ Comprehensive logging to file
✅ Backup before modification
✅ Official package sources (PPAs, repos)
✅ User permission validation
✅ Internet connectivity check
✅ Idempotent operations (safe to re-run)

---

## 📚 References

- [Docker Official Docs](https://docs.docker.com/engine/install/ubuntu/)
- [Oh My Zsh](https://ohmyz.sh/)
- [Neovim](https://neovim.io/)
- [Lazygit](https://github.com/jesseduffield/lazygit)
- [Lazydocker](https://github.com/jesseduffield/lazydocker)
- [Zoxide](https://github.com/ajeetdsouza/zoxide)
- [UV](https://github.com/astral-sh/uv)

---

## 📄 License

This project is licensed under the MIT License.

---

**Author**: typhoon1217  
**Date**: 2025-01-22  
**Version**: 1.0.0  

---

## 💡 Quick Reference

```bash
# First time use
chmod +x ubuntu-server-setup.sh && ./ubuntu-server-setup.sh

# Full automatic
./ubuntu-server-setup.sh -y

# View logs
cat ~/ubuntu-setup-*.log

# Refresh Docker permissions
newgrp docker

# Test all tools
docker run hello-world && lazygit --version && nvim --version
```

**Happy Coding! 🚀**
