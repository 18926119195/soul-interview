# 内部文档 · 严禁发给甲方

> 本文档仅供你方团队使用。交付甲方时**只发** [`CLIENT-交付说明.md`](./CLIENT-交付说明.md) 中的内容。

---

## 一、绝对不要交给甲方

| 类别 | 路径 / 内容 | 泄漏后果 |
|------|-------------|----------|
| Dify 控制台账号 | 登录邮箱 + 密码 | 可直接看到全部 prompt、知识库、工作流 |
| Dify 工作流导出 | `非线性自传创作 (9).yml` | 含 LLM 2/3 完整 system prompt |
| 知识库文件 | Dify 数据集导出、原始题库 | 核心 IP 完整泄露 |
| 环境变量 / Key | `.env.local`、SiliconFlow/DeepSeek Key | 可滥用推理、可调 Dify API |
| **开发用** Dify Key | `app-PkHQ...efYY`（见本机 `.env.local`） | 与演示 Key 混用会导致无法单独撤销 |
| 源码仓库 | GitHub / GitLab 链接（若含 YAML 或 env） | 可能含 prompt 或密钥历史 |
| Dify 公网后台地址 | `https://xxx.trycloudflare.com`（Tunnel URL） | 含登录页；若密码弱或被拿到链接，后台有风险 |

---

## 二、可以交给甲方

| 类别 | 说明 |
|------|------|
| 演示站 URL | Vercel 地址，如 `https://xxx.vercel.app` |
| 访问密码 | `DEMO_ACCESS_PASSWORD`（仅进演示站，不是 Dify Key） |
| 简短使用说明 | 见 `CLIENT-交付说明.md` |

---

## 三、演示专用 Key 管理

| Key | 用途 | 存放位置 |
|-----|------|----------|
| `app-PkHQ...` | **本地开发** | 本机 `autobio-web/.env.local` |
| `app-LtQg...` | **Vercel 演示站** | 仅 Vercel 环境变量，**不要写进 Git** |

演示结束后：在 Dify「API 密钥」页面**删除**演示 Key。

---

## 四、交付前自检（30 秒）

- [ ] 甲方仅有 URL + 演示密码，无 Dify 账号
- [ ] 未发送 YAML、知识库、`.env`、仓库链接
- [ ] 演示站 `DIFY_API_KEY` 为演示专用 Key，非开发 Key
- [ ] 浏览器 Network 中 `/api/chat` 响应无 prompt、无 Key
- [ ] Dify 应用未开启「公开嵌入 / 可编辑分享链接」
- [ ] **Tunnel URL 未发给甲方**（只填在 Vercel `DIFY_API_BASE`）

---

## 五、Cloudflare Tunnel 演示（当前方案）

```bash
# 演示前（保持运行）
./scripts/start-dify-tunnel.sh

# 演示后
# Ctrl+C 停止 Tunnel
```

详见 [`DEMO-DEPLOY.md`](./DEMO-DEPLOY.md)。

---

## 六、演示结束后

1. **Ctrl+C** 停止 `scripts/start-dify-tunnel.sh`（关闭 Tunnel）  
2. 删除 Dify 演示 API Key  
3. 下线 Vercel 或更换 `DEMO_ACCESS_PASSWORD`  
4. （可选）清理 Dify 演示期间 conversation 记录  
