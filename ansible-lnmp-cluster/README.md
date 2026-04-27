## Ansible LNMP 高可用集群自动化部署

### 项目简介

本项目使用 Ansible 实现 LNMP 高可用集群的一键部署。基于两台 Web 后端（Nginx + PHP-FPM）、一台 MySQL 数据库、一台 Nginx 反向代理的架构，通过 Ansible Role 模块化管理所有配置，支持敏感信息加密和幂等执行。

### 架构

```
客户端 (100.0.0.10)
    │
    ▼
代理层 (proxy / 50.0.0.5)
Nginx 反向代理 + 负载均衡
    │
    ├──── web1 (50.0.0.101)
    │     Nginx + PHP-FPM + MySQL
    │
    └──── web2 (50.0.0.102)
          Nginx + PHP-FPM
```

### 核心成果

- 四个独立 Role：`mysql`、`nginx_web`、`php`、`nginx_proxy`
- 负载均衡与健康检查：后端故障时自动切换，无 502
- 敏感信息加密：`ansible-vault` 管理数据库密码
- 幂等性验证：多次执行 Playbook 状态一致

### 项目结构

```
ansible-lnmp/
├── site_lnmp.yml              # 入口剧本
├── group_vars/all.yml         # 公共变量（加密）
├── inventory
└── roles/
    ├── mysql/                 # MariaDB 安装与配置
    ├── nginx_web/             # 后端 Web 服务
    ├── php/                   # PHP-FPM 配置
    └── nginx_proxy/           # 反向代理与负载均衡
```

### 快速开始

```bash
# 1. 确保 inventory 中主机 IP 正确
# 2. 执行部署
ansible-playbook site_lnmp.yml --ask-vault-pass

# 3. 验证
curl http://<proxy-ip>/
```

### 技术栈

- Rocky Linux 8.6
- Ansible（Role / Vault / 变量管理）
- Nginx（反向代理 / 负载均衡 / 动静分离）
- PHP-FPM（Unix Socket）
- MariaDB
