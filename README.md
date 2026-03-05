# Emby Worker Proxy（Cloudflare Workers + D1）

基于 Cloudflare Workers 的 Emby 前后端分离反代。  
支持后台管理（`/admin`）、节点管理、分流/直连兼容与 D1 持久化存储。

---

## 功能特性

- 管理后台：`/admin`
- 节点增删改查（D1 存储）
- 普通 / 分离模式
- 第三方播放器链路兼容
- 可选直连模式（按需开启）
- 节点缓存与列表缓存优化

---

## 快速部署

### 1）创建 D1 数据库

在 Cloudflare 控制台创建 D1 数据库（例如：`emby-proxy`）。

### 2）初始化数据表

在 D1 控制台依次执行以下 SQL：

`CREATE TABLE IF NOT EXISTS proxy_kv (k TEXT PRIMARY KEY, v TEXT NOT NULL, updated_at INTEGER NOT NULL);`

`CREATE INDEX IF NOT EXISTS idx_proxy_kv_k ON proxy_kv(k);`

### 3）上传 Worker 代码

将仓库内 `worker.js` 部署到 Cloudflare Workers。

### 4）绑定 D1

在 Worker → 设置 → 绑定 中添加 D1：

- 变量名：`EMBY_D1`
- 数据库：你创建的 D1

### 5）配置环境变量

在 Worker → 设置 → 变量 中添加：

- `ADMIN_TOKEN`（必填）

可选变量：

- `ENABLE_DIRECT_PROXY`：`0` 或 `1`（默认 `0`）
- `RAW_ALLOW_HOSTS`：直连白名单，逗号分隔
- `CAPY_STRIP_EMBY`：兼容性开关（默认 `0`）
- `CORS_ALLOW_ORIGIN`：自定义 CORS 来源（可留空）

### 6）访问后台

打开：`https://你的域名/admin`  
使用 `ADMIN_TOKEN` 登录后即可添加节点。

---

## 安全建议

- 不要把真实 `ADMIN_TOKEN`、数据库 ID、私有域名提交到公开仓库
- 建议定期轮换 `ADMIN_TOKEN`
- 公网服务建议配合访问控制策略（如 WAF / Access）

---

## 免责声明

本项目仅供学习与技术测试使用。请遵守当地法律法规及服务条款，使用风险由使用者自行承担。

---

## License

MIT
