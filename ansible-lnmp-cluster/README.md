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
    ├──── web1 (50.0.0.101)   （50.0.0.101）
    │     Nginx + PHP-FPM + MySQL│Nginx PHP-FPM MySQL
    │
    └──── web2 (50.0.0.102)   2.2.10 （2.2.10）
          Nginx + PHP-FPM   Nginx PHP-FPM
```

### 核心成果

- 四个独立 Role：mysql、nginx_web、php、nginx_proxy
- 负载均衡与健康检查：后端故障时自动切换，无 502
- 敏感信息加密：`ansible-vault` 管理数据库密码
- 幂等性验证：多次执行 Playbook 状态一致

### 项目结构

```mermaid
graph TD
    A[客户端<br>100.0.0.10]
    
    A -->|HTTP 请求| B   A—>|HTTP
    
    subgraph 代理层
        B[proxy<br>100.0.0.5 / 50.0.0.5<br>Nginx 反向代理<br>负载均衡 + 健康检查]
    end
    
    B -->|分发流量| C
    B -->|分发流量| D
    
    subgraph Web 后端集群
        C[web1<br>50.0.0.101<br>Nginx + PHP-FPM<br>MariaDB]
    end
    
    subgraph Web 后端集群
        D[web2<br>50.0.0.102<br>Nginx + PHP-FPM]
    end
```

### 快速开始

```bash   ”“bash   “bash”;“bash
# 1. 确保 inventory 中主机 IP 正确
# 2. 执行部署
ansible-playbook site_lnmp.yml --ask-vault-passansible-playbook site_lnmp。yml——ask-vault-pass

# 3. 验证
curl http://<proxy-ip>/   curl http://< proxy-ip> /
```

### 技术栈

- Rocky Linux 8.6
- Ansible（Role / Vault / 变量管理）
- Nginx（反向代理 / 负载均衡 / 动静分离）
- PHP-FPM（Unix Socket）   PHP-FPM（Unix Socket）
- MariaDB   ——MariaDB
