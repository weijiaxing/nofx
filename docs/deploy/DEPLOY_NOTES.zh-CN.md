# 部署备注（本地整理）

这份文档记录一次在 Ubuntu 24.04 上使用 Docker 部署 NOFX 的最小可行流程，方便后续复现。

## 适用环境

- Ubuntu 24.04
- 已有 root 或 sudo 权限
- 通过 Docker 部署

## 安装依赖

```bash
apt-get update
apt-get install -y docker.io docker-compose-v2 curl jq
systemctl enable --now docker
```

## 启动方式

在项目根目录执行：

```bash
docker compose -f docker-compose.prod.yml up -d
```

检查状态：

```bash
docker compose -f docker-compose.prod.yml ps
curl http://127.0.0.1:8080/api/health
```

## .env 注意事项

仓库中的 `.env.example` 不能直接用于生产运行，至少需要正确设置：

- `JWT_SECRET`
- `DATA_ENCRYPTION_KEY`
- `RSA_PRIVATE_KEY`

其中 `RSA_PRIVATE_KEY` 建议写成一行字符串，并保留 `\n` 转义；如果通过 Docker `env_file` 加载，建议整体加双引号，避免解析异常。

示例生成方式：

```bash
openssl rand -base64 32
openssl genrsa 2048
```

## 访问说明

- 本机访问：`http://127.0.0.1:3000`
- 局域网/私网访问：使用机器内网 IP
- 公网访问：使用服务器公网 IP，并确认安全组/防火墙已放行 3000 端口

## 风险提示

如果用于真实交易：

- 只给交易所 API 最小权限
- 不要开启提币权限
- 尽量使用子账户
- 先小额测试
- 公网长期运行建议补 HTTPS
