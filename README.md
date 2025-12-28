# TavernRegister

简介
----
TavernRegister 是一个极简的独立注册门户，可在不修改 SillyTavern 核心代码的情况下批量创建用户账号。后端会以管理员身份调用 SillyTavern 的内部 API（`/api/users/create`），前端界面与提示为中文，内置基础的输入校验与日志输出，便于在多用户环境下快速开放注册入口。

工作原理
----
1. 读取 `.env` 中的管理员凭证与 SillyTavern 地址，提前建立管理会话。
2. 注册时将提交的显示名称、用户标识、密码发送到 SillyTavern。
3. 调用 `/api/users/create` 创建账号，并返回登录入口信息。

演示地址
----
https://st.zkjd.me

快速开始（使用仓库内启动脚本）
----
本项目包含平台对应的启动脚本：Unix/macOS 使用 `start.sh`。优先使用仓库自带脚本来安装依赖并启动服务，脚本已包含常见检查并能简化部署流程。

在 Unix / Linux / macOS 环境：

```bash
# 赋予执行权限（首次运行时）
chmod +x start.sh
./start.sh
```

启动后，默认监听 `PORT`（默认 3070），浏览器访问：

http://localhost:3070/

有关生产部署（systemd / pm2 / Nginx 反向代理）请参阅上文的“服务器部署”小节，其中包含 systemd 单元示例、pm2 启动方法以及 Nginx 配置片段。

`.env` 配置说明
----
| 变量 | 说明 |
| --- | --- |
| `SILLYTAVERN_BASE_URL` | SillyTavern 服务完整地址，必须包含协议与端口，例如 `https://example.com:8000` |
| `SILLYTAVERN_ADMIN_HANDLE` | 具备创建用户权限的 SillyTavern 管理员账号 |
| `SILLYTAVERN_ADMIN_PASSWORD` | 上述管理员对应的密码 |
| `PORT` | TavernRegister 对外监听端口，默认 `3070` |

示例 `.env`
----
```env
SILLYTAVERN_BASE_URL=https://your-tavern-domain.com:5000
SILLYTAVERN_ADMIN_HANDLE=admin
SILLYTAVERN_ADMIN_PASSWORD=changeme
PORT=3070

```

重要约束
----
- 密码为必填项，前后端均会验证两次输入一致且不得为空。
- SillyTavern 基础信息通过官方 API 操作，不直接改动酒馆的数据文件。

排错指引
----
- 所有关键错误会输出到启动终端，包括管理员登录失败等，方便定位。
- 常见问题：
   - **403 管理员验证失败**：确认管理员账号/密码无误，`SILLYTAVERN_BASE_URL` 正确且能获取完整的 session cookie（含签名）。
   - **请求 4xx/5xx**：检查 TavernRegister 与 SillyTavern 是否能够相互访问、网络代理是否放行。
