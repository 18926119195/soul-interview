# 卖设备前 72 小时 · 保命清单

> **目标**：卖电脑后，产品仍能演示 / 能改代码 / 核心 IP 不丢。  
> **原则**：先保命（备份 + 脱钩本机），再谈扩人。没钱上 VPS 就**主动暂停对外演示**，不要硬撑。

---

## 时间轴怎么排

| 时段 | 做什么 |
|------|--------|
| **第 1 天（0–24h）** | 备份 IP + 代码上云 + 密码清单 |
| **第 2 天（24–48h）** | VPS 买机 + 迁 Dify（或决定暂停演示） |
| **第 3 天（48–72h）** | Vercel 切 VPS + 冒烟测试 + 卖设备前最后一遍验收 |

每天结束打勾：**演示链接能聊通一句 / 代码在云端能拉到 / YAML 有第二份备份**。

---

## A. 第 1 天：备份（0 成本，必做）

### A1. 核心 IP 三件套（至少 2 个位置各存 1 份）

- [ ] **工作流**：`非线性自传创作 (1).yml`  
  - 复制到：U 盘 / 网盘 / 邮箱发给自己  
- [ ] **知识库**：在 Dify 控制台导出数据集（若有原始文件一并打包）  
- [ ] **密钥清单**（单独加密文件，勿进 Git）：  
  - 本地开发 Dify Key（`.env.local`）  
  - Vercel 演示 Key（`app-LtQg...`）  
  - `DEMO_ACCESS_PASSWORD`  
  - SiliconFlow / DeepSeek 模型 Key  
  - Vercel 账号、Dify 管理员密码  

推荐再建一个 **`SECRETS-仅自己.md`**（或 1Password / 备忘录），卖设备前填完，卖后靠它恢复。

### A2. 代码上云（必做）

当前项目**还没有 Git 仓库**，卖设备前务必完成：

```bash
cd "/Users/yueqiu/非线性前端设计"

# 根目录 .gitignore（勿提交密钥和 YAML）
cat > .gitignore << 'EOF'
.env
.env.*
!.env*.example
*.local
非线性自传创作*.yml
*.yml
node_modules/
.next/
.DS_Store
.tunnel-url
.tunnel.log
EOF

git init
git add autobio-web docs scripts
git add .gitignore
git commit -m "chore: initial backup before device sale"

# GitHub 新建私有仓库后：
# git remote add origin git@github.com:你的用户名/autobio-private.git
# git push -u origin main
```

- [ ] GitHub 仓库设为 **Private**  
- [ ] 确认 **没有** 提交 `.env.local`、`*.yml`、`.tunnel-url`  
- [ ] 在另一台设备或网页上能 `git clone` 成功  

### A3. 演示站信息归档

- [ ] 演示 URL：`https://autobio-web-ivory.vercel.app`  
- [ ] Vercel 项目：`yueqiu-autobiography/autobio-web`  
- [ ] 记录 Vercel 环境变量名称（值在 Vercel 控制台）：  
  `DIFY_API_BASE` / `DIFY_API_KEY` / `DEMO_ACCESS_PASSWORD` / `DEMO_SESSION_SECRET`  

### A4. 本机 Dify 可迁移性自检

- [ ] 浏览器能打开 `http://localhost` 且工作流已发布  
- [ ] 用本地 `autobio-web` 能完整走通：开场 → 四隧道 → 进入阶段二  
- [ ] 截图保存当前 Dify「应用已发布」状态（卖设备后对照用）  

---

## B. 第 2 天：脱离本机（二选一）

### 方案 1：有 ¥40–80 → 上 VPS（推荐，卖设备后仍能 7×24 演示）

#### B1. 买机

- [ ] 阿里云 / 腾讯云 **轻量 2核4G**，Ubuntu 22.04  
- [ ] 安全组放行：22（SSH）、80、443  
- [ ] 域名（可选）：`api.你的域名.com` → VPS IP  

#### B2. 装 Dify（官方 Docker）

```bash
# SSH 登录 VPS 后（示例，以 Dify 官方文档为准）
sudo apt update && sudo apt install -y docker.io docker-compose-plugin
git clone https://github.com/langgenius/dify.git
cd dify/docker
cp .env.example .env
# 编辑 .env：域名、密码等
docker compose up -d
```

- [ ] Dify 控制台可访问  
- [ ] **改默认管理员密码**（强密码）  
- [ ] 控制台 **不要** 对公网裸奔：仅 HTTPS + 强密码，或 IP 白名单  

#### B3. 迁移应用

- [ ] 导入 `非线性自传创作 (1).yml`  
- [ ] 重新挂载知识库  
- [ ] 配置 SiliconFlow / DeepSeek 模型 Key  
- [ ] 新建 **VPS 专用 API Key**（与本地、旧演示 Key 分开）  
- [ ] 在 VPS 上跑一遍完整流程自测  

#### B4. HTTPS

- [ ] 用 Nginx + Certbot 或 Dify 自带方式，得到：  
  `https://api.你的域名.com`  
- [ ] API 地址记为：`https://api.你的域名.com/v1`  

---

### 方案 2：没钱上 VPS → 主动暂停演示（同样负责任）

- [ ] Vercel 删除或清空 `DIFY_API_BASE` / `DIFY_API_KEY`（或整站下线）  
- [ ] 给已发过链接的人发一句：「体验版维护中，X 日后恢复」  
- [ ] **不要** 卖设备后还让别人测 Tunnel——你会失联，对方只会觉得你产品不行  

卖设备后你有代码 + YAML 备份，找网吧/二手本仍可开发；只是**不能 7×24 在线服务**。

---

## C. 第 3 天：Vercel 切换 + 验收

### C1. 更新 Vercel（仅方案 1）

```bash
cd autobio-web

# 示例：把 DIFY_API_BASE 换成 VPS
printf '%s' 'https://api.你的域名.com/v1' | npx vercel env rm DIFY_API_BASE production -y
printf '%s' 'https://api.你的域名.com/v1' | npx vercel env add DIFY_API_BASE production

# 演示 Key 换成 VPS 上新 Key
# npx vercel env rm DIFY_API_KEY production -y
# printf '%s' 'app-新Key' | npx vercel env add DIFY_API_KEY production

npx vercel deploy --prod --yes
```

- [ ] `npx vercel env ls` 四个变量都在 Production  
- [ ] **停止本机 cloudflared**（不再依赖 Tunnel）  

### C2. 冒烟测试（卖设备前最后一遍）

- [ ] 无痕窗口打开 `https://autobio-web-ivory.vercel.app`  
- [ ] 访问密码能登录  
- [ ] 发一句「你好」→ 能收到回复（允许 30–120 秒）  
- [ ] 出现四隧道 → 点一条 → 进入阶段二  
- [ ] **关掉本机 Dify Docker** 再测一次 → 必须仍能用（证明已不依赖本机）  

### C3. 卖设备当日

- [ ] 本机磁盘敏感文件删除或加密（`.env.local`、YAML 若不需要可只留云端）  
- [ ] 确认手机能：登录 GitHub、Vercel、云厂商控制台  
- [ ] 把 `SECRETS-仅自己.md` 存到 **手机 + 网盘** 两处  

---

## D. 卖设备后的最低成本运维（每月）

| 项目 | 动作 | 费用 |
|------|------|------|
| VPS | 续费一台，看 Docker 是否在跑 | ¥40–80 |
| Vercel | 一般不用管 | ¥0 |
| 模型 | 设计伙伴阶段用 **BYOK**（后续再做） | 你 ¥0 |
| 改代码 | 任意电脑 `git clone` → 改 `autobio-web` → `vercel deploy` | ¥0 |

**每周 10 分钟例行：**

1. 打开演示站发一句测试  
2. VPS 上看 `docker compose ps`  
3. 看模型 API 余额（若你还在补贴少数人）  

---

## E. 故障速查

| 现象 | 可能原因 | 处理 |
|------|----------|------|
| 500 未配置 DIFY_API_BASE | Vercel 环境变量丢了 | `vercel env ls` → 补全 → redeploy |
| 502 服务不可用 | VPS Dify 挂了 / Tunnel 还在但本机关了 | 上 VPS；别再用 Tunnel |
| 登录密码不对 | 改过 `DEMO_ACCESS_PASSWORD` 没同步给测试者 | Vercel 查看或重置 |
| 四隧道不出 | 知识库未挂载 / 工作流未发布 | VPS Dify 后台检查 |

---

## F. 72 小时总勾选（一页纸）

```
第 1 天
[ ] YAML + 知识库 + 密钥 双备份
[ ] Git 私有仓库 push 成功
[ ] 密钥未进 Git

第 2 天
[ ] 方案选定：VPS / 或暂停演示
[ ] （VPS）Dify 导入 + 自测通过

第 3 天
[ ] Vercel 指向 VPS（或已暂停）
[ ] 关本机 Dify 后演示仍可用（或已通知维护）
[ ] 手机能管 GitHub + Vercel + 云控制台
```

---

**下一步**：设计伙伴怎么招募、问什么 → 见 [`DESIGN-PARTNERS-10.md`](./DESIGN-PARTNERS-10.md)
