# 甲方演示部署指南 · Cloudflare Tunnel + Vercel（零 VPS 费用）

> 内部文档：[`CONFIDENTIAL-不给甲方.md`](./CONFIDENTIAL-不给甲方.md)  
> 发给甲方：[`CLIENT-交付说明.md`](./CLIENT-交付说明.md)

---

## 架构

```
甲方浏览器
    │  只知道 demo.vercel.app + 访问密码
    ▼
Vercel（autobio-web）
    │  DIFY_API_BASE = https://xxx.trycloudflare.com/v1  （仅服务端）
    │  DIFY_API_KEY  = 演示专用 Key
    ▼
Cloudflare Tunnel（本机运行，免费）
    ▼
本机 Dify（Docker，localhost:80）
    ├── 工作流 + prompt + 知识库（甲方看不到）
    └── 你的 SiliconFlow / DeepSeek Key
```

**演示期间你需要保持运行：**

1. 本机 Dify（Docker）
2. `./scripts/start-dify-tunnel.sh`
3. Vercel 已部署的 autobio-web

---

## 第 0 步：前置条件

- [ ] 本机 Dify 已跑通（浏览器能打开 `http://localhost`）
- [ ] 已按 [`CONFIG-配置清单.md`](./CONFIG-配置清单.md) 导入 `非线性自传创作 (9).yml`、挂载故事库、发布
- [ ] 已在 Dify 创建**演示专用 API Key**（与本地开发 Key 分开）
- [ ] 已安装 cloudflared：`brew install cloudflared`
- [ ] 有 GitHub + [Vercel](https://vercel.com) 账号（免费）

---

## 第 1 步：启动 Cloudflare Tunnel

在项目根目录：

```bash
chmod +x scripts/start-dify-tunnel.sh scripts/check-dify-tunnel.sh
./scripts/start-dify-tunnel.sh
```

终端会出现类似：

```
https://random-words.trycloudflare.com
```

记下这个地址。**不要发给甲方。**

若 Dify 不在 80 端口（少见），指定本地地址：

```bash
DIFY_LOCAL_URL=http://localhost:80 ./scripts/start-dify-tunnel.sh
```

另开终端验证：

```bash
./scripts/check-dify-tunnel.sh
```

Vercel 里应配置：

```env
DIFY_API_BASE=https://random-words.trycloudflare.com/v1
```

> **注意**：Quick Tunnel 每次重启 URL 可能变化 → 变化后需更新 Vercel 环境变量并 Redeploy。

**保持此终端窗口不要关**，关 = 甲方无法使用演示站。

---

## 第 2 步：部署 autobio-web 到 Vercel

### 2.1 推送代码

将 `autobio-web` 推到 GitHub（根目录 `.gitignore` 已忽略 YAML、`.env.local`、知识库）。

Vercel Import 项目时 **Root Directory** 选：`autobio-web`。

### 2.2 配置环境变量

Vercel → Project → Settings → Environment Variables：

| 变量 | 值 | 说明 |
|------|-----|------|
| `DIFY_API_BASE` | `https://你的tunnel.trycloudflare.com/v1` | 来自第 1 步，**勿给甲方** |
| `DIFY_API_KEY` | `app-演示专用Key` | Dify「访问 API」新建 |
| `DEMO_ACCESS_PASSWORD` | 自定强密码 | **发给甲方** |
| `DEMO_SESSION_SECRET` | `openssl rand -hex 32` | 仅 Vercel |

### 2.3 部署

Deploy 完成后得到 URL，例如 `https://autobio-web.vercel.app`。

---

## 第 3 步：自测

1. **无痕窗口**打开 Vercel URL → 输入 `DEMO_ACCESS_PASSWORD` → 进入主界面  
2. 发「最近有点想聊聊大学的事」→ 等四隧道卡片  
3. 点击隧道 → 进入阶段二  
4. DevTools → Network → `/api/chat` 只有 `answer` / `conversationId` / `messageId`  
5. 确认甲方**没有**收到 trycloudflare.com 链接  

---

## 第 4 步：交付甲方

编辑 [`CLIENT-交付说明.md`](./CLIENT-交付说明.md)，只发：

- 体验地址（Vercel URL）  
- 访问密码  
- 体验期限  

**不要发：** Tunnel URL、Dify 地址、API Key、YAML、知识库。

---

## 演示前 / 演示后检查

### 演示前

- [ ] Dify Docker 运行中  
- [ ] Tunnel 脚本运行中，URL 已写入 Vercel  
- [ ] Vercel 使用演示专用 Key  
- [ ] `DEMO_ACCESS_PASSWORD` 已设置  
- [ ] Dify 管理员强密码，无甲方账号  

### 演示后

- [ ] `Ctrl+C` 停止 Tunnel  
- [ ] Dify 删除演示 API Key  
- [ ] （可选）Vercel 更换密码或暂停项目  

---

## 常见问题

**Q: Tunnel URL 变了怎么办？**  
A: 更新 Vercel 的 `DIFY_API_BASE` → Redeploy。演示期间尽量保持 Tunnel 进程不关。

**Q: 甲方会看到我 Dify 登录页吗？**  
A: 不会，除非你把 trycloudflare.com 链接发给他们。他们只有 Vercel 演示站。

**Q: 502 服务不可用？**  
A: 检查 Dify 是否运行、Tunnel 是否关、Vercel 的 `DIFY_API_BASE` 是否与当前 Tunnel URL 一致。

**Q: 本机 `.env.local` 要改吗？**  
A: 不用。本地继续 `http://localhost/v1` + 开发 Key；Tunnel 只给 Vercel 用。

**Q: 想长期稳定在线？**  
A: 再考虑 VPS 方案；短期甲方测试 Tunnel 足够。

---

## 其他方案（备用）

| 方案 | 何时用 |
|------|--------|
| VPS + HTTPS | 长期 7×24、不能本机常开 |
| Dify 云版 | 不想本机跑 Dify |
| 屏幕共享 | 完全不想 Dify 上公网 |
