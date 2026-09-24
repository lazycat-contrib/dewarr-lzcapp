# Dewarr - LazyCat App

[Dewarr](https://github.com/logabell/dewarr)（自托管有声书发现与自动下载）的懒猫微服（LazyCat）打包。

## 模式

- **Docker 镜像应用（mirror 模式）**：`dewarr` 镜像经 `ghcr.1ms.run` 加速器拉取（digest 校验）；`postgres` 固定 `docker.1ms.run/postgres:18`。
- **自动跟随上游**：每日检查上游 `ghcr.io/logabell/dewarr` 的 semver tag（`channel: stable`），发现新版本自动更新版本号与镜像、构建 LPK、发布商店。
- **喵喵商店发布**：仅私有商店（MiaoMiao private store）。

## 配置要点

| 项 | 值 |
| --- | --- |
| 包名 | `community.lazycat.app.dewarr` |
| 子域名 | `dewarr` |
| 数据卷 | `/lzcapp/var/dewarr/config` → `/config`；`/lzcapp/var/dewarr/data` → `/data`；`/lzcapp/var/postgres` → PostgreSQL 数据 |
| 健康检查 | `GET /api/health/ready` |
| 对外地址 | `PUBLIC_URL=https://${LAZYCAT_SUBDOMAIN}.${LAZYCAT_BOX_DOMAIN}` |
| 数据库 | PostgreSQL 18，密码 `{{ stable_secret "db_password" }}` 自动生成 |

## 免密登录

应用自带账号体系（首次打开创建管理员），已配置三段式自动填充：

1. **捕获**：`POST /api/auth/bootstrap` 请求中记录用户名/密码
2. **持久化**：创建成功后写入 `ctx.persist`
3. **回填**：登录表单自动填充（`autoSubmit: false`，需手动点击登录）

## 结构

| 文件 | 说明 |
| --- | --- |
| `package.yml` | 包元数据 |
| `lzc-manifest.yml` | 服务、路由与注入配置 |
| `lzc-build.yml` | 构建配置 |
| `icon.png` | 图标 |
| `.github/lazycat-action.yml` | [lazycat-github-action](https://github.com/ca-x/lazycat-github-action) 配置 |
| `.github/workflows/lazycat.yml` | 构建 + 发布工作流 |

## 所需 Secrets

| Secret | 说明 |
| --- | --- |
| `APPSTORE_URL` / `APPSTORE_TOKEN` | 喵喵商店 API 地址与发布令牌 |
| `APP_ID` | 可选 |
| `PRIVATE_STORE_GROUP_CODES` | 可选，私有分组码 |

## 许可

MIT（上游）
