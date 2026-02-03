# 配置指南

本章节详细介绍 Oh My OpenCode Agents 的各项配置选项。

## 本章节内容

- **[环境变量](./environment.md)** - 所有可用的环境变量及其说明
- **[自定义配置](./customization.md)** - 如何自定义和扩展配置

## 配置层次

项目支持多层配置：

1. **默认配置** - 项目内置的推荐配置
2. **环境变量** - 通过 `.env` 文件或系统环境变量覆盖
3. **自定义脚本** - 在 `custom/` 目录中添加额外配置

## 快速配置

创建 `.env` 文件：

```bash
# 基础配置
SERVER_IP=your_server_ip
DOMAIN=your_domain.com

# 组件选择
INSTALL_OPENCODE=true
INSTALL_BT_PANEL=false
INSTALL_NGINX=true

# 高级选项
AUTO_MODE=true
SKIP_CONFIRMATIONS=true
```

## 注意事项

!!! warning "重要提示"
    修改配置前请备份重要数据。某些配置更改可能需要重新安装组件。
