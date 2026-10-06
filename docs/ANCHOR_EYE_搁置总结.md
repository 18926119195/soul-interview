# anchor-eye（目击径）搁置总结

> 状态：**搁置**（2026-07-27）  
> 目录：`anchor-eye/`  
> 入口：`npm run dev` → http://localhost:3456  
> 与 `autobio-web`（非线性自传）**完全分离**，勿混用环境变量与端口。

---

## 1. 这个项目想做什么

独立 MVP：**眼睛（Eye）+ 同伦身份**——让「原件 / 用户 / AI」三层可区分、可连边。

核心不是让 AI 改写原文，而是：

1. 用户上传 PDF（含扫描件）
2. 系统把原件切成**可读碎片（Shard）**
3. 用户说出直觉 / 提问
4. AI **只挑选与重排**已有碎片，不重写正文
5. 在 3D「碎片空间」里按页序 / 相关度 / 线索排序阅读

灵感来自 Ted Nelson 的文档宇宙 / CVS（连接可视化）：在同一空间里拉近拉远看原件，而不是另开摘要窗。

---

## 2. 三层与关键概念

| 概念 | 含义 |
|------|------|
| **原件层** | PDF 页像素 + 文字层 / OCR 文本；权威来源 |
| **用户层** | 直觉、提问、选区、路径 |
| **AI 层** | 调度：选碎片、打分、给 label/why、建议 thread；**不当原文替身** |
| **眼睛（Eye）** | 对准原件某段的视口，不是摘要卡片 |
| **Canonical** | 真源锚（某页某段 offset） |
| **Appearance** | Canonical 在 UI 上的显现（眼睛、路径命中等） |
| **Witness** | 可核验：offset / hash，证明 Appearance 指的是哪段 Canonical |
| **同伦** | 跨语境「是同一段」的可验证连边规则，而非模糊相似 |

---

## 3. 已实现到哪一步

### 3.1 能跑的主链路

- Next.js 14 App Router，端口 **3456**
- PDF 打开（`pdfjs-dist`）+ 页索引（文字层优先，扫描件走 `tesseract.js` OCR）
- **文字几何 `TextRun`**：OCR / 文字层抽取时，每个词带归一化 bbox，绑定字符区间 `[start,end)`（「绳子」原件端）
- **碎片切割**（`src/lib/shard.ts`）：按行距 / 换栏启发式切段落级 Shard；无几何时退回纯文本切分（`region` 为空）
- **碎片空间**（`ShardSpace` + `ShardCard`）：3D 排版（长卷 / 网格墙 / 星系），相机平移 / 倾斜 / 滚轮缩放
- **区域裁切渲染**：有 `region` 时 `renderPageRegionToCanvas` 裁切原件像素，让碎片字号可读
- **AI 切并排**：`POST /api/cut-shards`（硅基流动 `zai-org/GLM-5.2`，见 `.env.local`）；无 Key 时本地关键词回退
- 旧页级在场检索仍保留：`POST /api/presence-search`（含 TOC 降权、动态补索引页）

### 3.2 「绳子」模型（最近一轮）

用户比喻：OCR 像手指划过文字，划过处留下记号；记号两端拴住「OCR 文本」与「原件坐标」——扯文本端应直接锚定原件，**禁止事后用文本回原件重找位置**。

已落地：

- `rectsForRange`：按字符偏移在已存 `runs` 上**切绳**（run 内线性插值），不 `indexOf` 重找
- Shard 携带自身 `runs`
- `ShardCard`「记号 N」按钮：在裁切像素上叠加高亮，可视化绳子原件端

仍存在的旧断绳（仅旧页级链路）：`findQuoteOffsets` 里的文本回找——碎片主链路不用它。

### 3.3 已知未修 / 卡点（搁置时）

1. **上传 PDF 可能失效**  
   隐藏的 `<input type="file">` 放在折叠的 `<details class="identity-inspector">` 内；主界面「上传 PDF」只是 `fileInputRef.current?.click()`。部分浏览器对「封闭 details 内的 file input」程序化点击会静默失败。  
   **建议续做**：把 file input 挪到 Studio 根级（details 外），并把 `pdfError` 显示到 `ShardSpace` 主区。

2. **演示文本路径**  
   默认 `createInitialStore()` 是纯文本「未寄出的信」：无 `pdfProxy`、无 `runs`、无 `region` → 只能显示白底纯文字，看不到裁切与「记号」。测绳子必须先成功上传 PDF。

3. **检索/切割准确度**  
   - 几何切分启发式可能切错（双栏、脚注、OCR 框偏）  
   - AI 只看到候选碎片截断文本（约 260 字），且候选有预过滤  
   - AI 只选已有 id，不生成坐标——错位多半是切绳粗或 OCR bbox，不是 AI「编坐标」

4. **README 过时**  
   仍写智谱 `glm-4-flash` / 「拉齐在场」页级流程；实际主 UI 已切到碎片空间 + 硅基流动。

5. **清晰度问题的产品结论**  
   整页放进 3D 很难「不放大就清晰」→ 已转向**段落级碎片裁切**；若仍不清，优先查 supersample / region 映射，而不是再开独立阅读窗（用户明确拒绝 Nelson 式「另开主视图」）。

---

## 4. 关键文件地图

| 路径 | 作用 |
|------|------|
| `src/components/Studio.tsx` | 总编排：上传、索引、切并排、状态 |
| `src/components/ShardSpace.tsx` | 3D 碎片宇宙 UI |
| `src/components/ShardCard.tsx` | 单碎片：像素裁切 + 记号 overlay |
| `src/lib/shard.ts` | Shard 类型与切分 |
| `src/lib/pageGeom.ts` | TextRun / PageRegion / `rectsForRange` |
| `src/lib/arrange.ts` | 排序与 3D pose |
| `src/lib/pdf.ts` | 加载、几何抽取、页/区域渲染 |
| `src/lib/ocr.ts` | OCR + 词级 bbox |
| `src/lib/indexPdf.ts` | 多页索引 / 补几何 |
| `src/app/api/cut-shards/route.ts` | AI 选碎片 + thread |
| `src/app/api/presence-search/route.ts` | 旧页级语义检索 |
| `src/lib/tocResolve.ts` | 目录页降权与正文页解析 |
| `public/samples/demo-pages.pdf` | 样例 PDF |

相关理念文档（仓库级，非本目录独占）：

- `docs/NELSON_PDF_工程落地把关_GLM5V.md`
- `docs/NELSON_双专利_灵魂地图改造差_GLM5V.md`
- `docs/灵魂地图_双专利改造差_落地版.md`（主要服务 `autobio-web` 灵魂地图）

---

## 5. 与非线性自传（autobio-web）的关系

| | anchor-eye | autobio-web |
|--|------------|-------------|
| 产品 | 目击径：PDF 原件眼睛 / 碎片宇宙 | 非线性自传：隧道对话 + 灵魂地图 |
| 端口 | 3456 | 3000 |
| AI | 硅基流动 / 本地回退 | Dify 工作流 |
| Nelson | PDF 文档宇宙 / 碎片裁切 | CVS 页空间、光束、span 连边（灵魂地图） |

**续作时**：不要把 anchor-eye 的 PDF 绳子逻辑直接塞进 autobio；两边共享的是理念（原件锚、span、不改写），不是同一套 store。

---

## 6. 下次重启清单（最小）

```bash
cd anchor-eye
# 确认 .env.local 有 SILICONFLOW / 对应 Key（以实际 env 为准）
npm run dev   # http://localhost:3456
```

优先修：

1. file input 移出 `<details>`，错误提示上提到碎片空间  
2. 上传样例 PDF，确认有「记号 N」与像素裁切  
3. 再谈 AI 切割精度 / 是否让模型参与「选 span 但仍只切已有 runs」

---

## 7. 一句话收束

**anchor-eye 已验证「原件碎片 + AI 只选排 + 3D 空间阅读」骨架，并把 OCR 记号做成可切的绳子；卡在上传入口可能失效、演示文本无几何、以及几何/OCR 精度。** 产品主战场暂切回 `autobio-web`；本仓库目录保留，待上传修复后再测绳子。
