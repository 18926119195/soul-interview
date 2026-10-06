# 非线性自传创作 · 配置清单（2026-07 版）

> 同行者已改为 **Dify 输出「人名 + 故事」**（`display_name` + `companion_story`）。  
> 按本清单顺序配置，可避免探望时只显示「同行者」或主题句当人名。

---

## 一、文件对照表

| 用途 | 路径 |
|------|------|
| Dify 工作流（导入此文件） | [`非线性自传创作 (9).yml`](../非线性自传创作%20(9).yml) |
| 故事库源文件（上传知识库） | [`非线性自传问题全集_故事库_通用分段.md`](../非线性自传问题全集_故事库_通用分段.md) |
| 前端环境变量模板 | [`autobio-web/.env.local.example`](../autobio-web/.env.local.example) |
| 校验脚本（与 yml 内 Python 节点同步） | [`autobio-web/scripts/dify-companion-validate.py`](../autobio-web/scripts/dify-companion-validate.py) |
| 人名索引生成（前端兜底，可选） | `cd autobio-web && node scripts/build-companion-name-index.mjs` |

---

## 二、Dify 后台（必做）

### 2.1 导入并发布工作流

1. Dify → **工作室** → **导入 DSL** → 选择 `非线性自传创作 (9).yml`
2. 若已有同名应用：选 **覆盖** 或新建后停用旧版
3. 检查应用模式为 **高级对话（advanced-chat）**
4. **发布** 新版本

### 2.2 知识库

1. 新建数据集（或复用现有），名称建议：`故事库_通用分段`
2. 上传 [`非线性自传问题全集_故事库_通用分段.md`](../非线性自传问题全集_故事库_通用分段.md)
3. 分段方式建议：**按自定义分隔符** `<<<BLOCK>>>`
4. 完成嵌入（Embedding 模型与 yml 中一致或等价，如 Qwen3-Embedding）

### 2.3 重新绑定知识检索节点（导入后必查）

YAML 里的 `dataset_ids` 是本机 ID，**换环境后必须手动改**：

| 节点名称 | 作用 | 应绑定的数据集 |
|----------|------|----------------|
| **知识检索** | 阶段一选隧道 | 你的故事库数据集 |
| **故事知识检索** | 进隧道 / 同行者匹配 | **同上故事库** |
| （若有第三个检索节点） | 按工作流图确认 | 同上或题库集 |

操作：打开节点 → 选择数据集 → 保存 → **重新发布**。

### 2.4 模型与插件

工作流依赖（见 yml `dependencies`）：

- `langgenius/deepseek`（如 deepseek-v4-flash）
- `langgenius/siliconflow`（Embedding / Reranker）

在 Dify **设置 → 模型供应商** 中配置对应 API Key，并确认各 LLM 节点模型可用。

### 2.5 会话变量（导入后自动存在，仅核对）

| 变量名 | 说明 |
|--------|------|
| `selected_question` | 当前大主题 |
| `mood_text` | 用户心情文本 |
| `stage` | selecting / deepening |
| `companion_profile` | 同行者档案 JSON（含 display_name、companion_story） |
| `companion_state` | dormant / resonating / revealed |
| `companion_reveal_count` | 已探望次数，默认 0 |

### 2.6 创建 API Key

Dify 应用 → **访问 API** → 创建密钥 → 填入前端 `DIFY_API_KEY`。

建议：

- **本地开发** 一把 Key
- **演示 / Vercel** 另一把 Key（便于单独撤销）

### 2.7 同行者链路验收（Dify 调试）

1. 工作流 **预览 / 调试**
2. 模拟用户发送：`进入：在你的童年，有没有某一刻你被某样东西猛烈地吸引……`（任选一题）
3. 检查节点 **校验同行者JSON** 输出 `companion_init_json` 应含：

```json
{
  "type": "companion_init",
  "display_name": "梅纽因",
  "companion_story": "还不满四岁的梅纽因，常常被大人带去旧金山的柯伦剧院……",
  "story_source": "故事一",
  "reveal_text": "……",
  "theme_texture": "……"
}
```

4. 再测故事一无人名的块（如含「坏种子」的题）→ `display_name` 应为 **卡什**，`story_source` 为 **故事二**

---

## 三、前端 autobio-web（必做）

### 3.1 环境变量

```bash
cd autobio-web
cp .env.local.example .env.local
```

编辑 `.env.local`：

```env
# 本机 Dify
DIFY_API_BASE=http://localhost/v1
# 或 Cloudflare Tunnel：DIFY_API_BASE=https://xxxx.trycloudflare.com/v1

# 与 Dify「访问 API」中刚创建的 Key 一致
DIFY_API_KEY=app-xxxxxxxx
```

可选（反馈功能）：

```env
FEISHU_APP_ID=cli_xxx
FEISHU_APP_SECRET=xxx
FEISHU_CHAT_ID=oc_xxx
# 或 FEEDBACK_WEBHOOK_URL=...
```

### 3.2 安装与启动

```bash
npm install
npm run dev
```

打开 http://localhost:3000

### 3.3 导入 Dify 后前端必做的一次清理

旧会话里没有 `display_name` / `companion_story`。导入新 yml 并发布后：

1. 浏览器 **硬刷新**（`Cmd + Shift + R`）
2. 控制台执行（清空旧灵魂库）：

```javascript
localStorage.removeItem('autobio:soul-companions');
location.reload();
```

3. **重新进入**各隧道（「进入：{问题}」），让新 `companion_init` 写入本地

### 3.4 前端功能验收

| 步骤 | 预期 |
|------|------|
| 阶段一聊天 → 出现 4 隧道 | 正常 |
| 点击进入阶段二 | 右侧余温区出现 |
| 点「探望同行者」 | 列表显示 **人名**（非「同行者」、非主题句） |
| 点人名 | 显示 **故事正文** + 旁注 |
| 切换隧道 / 往期探索 | 各隧道独立会话与人名 |

---

## 四、演示部署（Vercel + Tunnel，可选）

详见 [`DEMO-DEPLOY.md`](./DEMO-DEPLOY.md)。

Vercel 环境变量至少：

```env
DIFY_API_BASE=https://你的tunnel.trycloudflare.com/v1
DIFY_API_KEY=app-演示专用key
DEMO_ACCESS_PASSWORD=甲方访问密码
```

演示前保持本机：**Dify Docker** + `./scripts/start-dify-tunnel.sh` 运行中。

---

## 五、修改工作流 Python 后的同步方式

若改了 `autobio-web/scripts/dify-companion-validate.py` 或 `dify-companion-reveal.py`：

1. 用脚本写回 yml，或 Dify 控制台 **手动粘贴** 到对应代码节点
2. **重新发布** Dify 应用
3. 前端无需改 env，但建议清 `autobio:soul-companions` 后重进隧道

---

## 六、常见问题

**Q: 探望仍显示「同行者」？**  
A: ① Dify 是否已发布新版 yml；② API Key 是否指向该应用；③ 是否重进隧道；④ 清 `autobio:soul-companions` 后重试。

**Q: companion_init 里没有 display_name？**  
A: 检查 **故事知识检索** 是否绑对数据集；检索结果是否含 `故事一：` / `故事二：`。

**Q: 本地改 yml 后 Dify 没变化？**  
A: 必须在 Dify 里 **重新导入或粘贴并发布**，改仓库文件不会自动同步到已运行的 Dify。

**Q: 502 / 超时？**  
A: 查 `DIFY_API_BASE` 是否可达（Tunnel 是否关）、模型 Key 是否有效。

---

## 七、原件绳 OCR（智谱 GLM-OCR）

与 `witness-cut` 同源配置：

| 项 | 值 |
|----|-----|
| 服务商 | 智谱 BigModel |
| 接口 | `https://open.bigmodel.cn/api/paas/v4/layout_parsing` |
| 模型 | `glm-ocr` |
| Key | `autobio-web/.env.local` 的 `ZHIPU_API_KEY`（可与 witness-cut 同一把） |
| 实现 | `src/lib/glmOcr.ts` |
| 入口 | 上传扫描 PDF → `POST /api/pdf-cut`（别名 `POST /api/cut`） |

数字 PDF 优先走浏览器文字层，无需 Key；无文字层的扫描页自动调 GLM-OCR。  
改 `.env.local` 后需 **重启** `npm run dev`。

---

## 八、最小检查清单（打印勾选）

```
[ ] 已导入 非线性自传创作 (9).yml 并发布
[ ] 故事库已上传 非线性自传问题全集_故事库_通用分段.md
[ ] 「故事知识检索」节点已绑定该数据集
[ ] DeepSeek / SiliconFlow 模型 Key 已配置
[ ] Dify API Key 已写入 autobio-web/.env.local
[ ]（可选）ZHIPU_API_KEY 已写入，扫描 PDF OCR 可用
[ ] npm run dev 可访问
[ ] 调试进隧道 → companion_init 含 display_name + companion_story
[ ] 已清 soul-companions 并重进隧道
[ ] 探望显示正确人名（如梅纽因、卡什）
```
