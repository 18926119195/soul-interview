# 灵魂的面试 — Soul's Interview

> 一份关于「求职 / 研究 / 自我探索」的真实对话档案 + 实战交付物。

本仓库是 **2026-10-06 (Tuesday)** 一天工作的小结，包含三个对话的 transcript、HR/岗位对的简历 / 研究文档等可交付物。**主要受众：用户本人复盘 + 后续求职流程使用**。

## 仓库结构

```
.
├── README.md                                              # 本文件
│
├── transcripts/                                           # 三段 Cursor 对话的原始 transcript
│   ├── 2026-10-06_bd5c9400_company-and-role-deep-read.transcript.txt
│   ├── 2026-10-06_bd5c9400_company-and-role-deep-read.summary.md
│   ├── 2026-10-06_50d8d604_notion-content-reading-and-github-push.transcript.txt
│   ├── 2026-10-06_50d8d604_notion-content-reading-and-github-push.summary.md
│   ├── 2026-10-06_e63b63b1_xiaohongshu-link-reading.transcript.txt
│   └── 2026-10-06_e63b63b1_xiaohongshu-link-reading.summary.md
│
├── cv-pro-local/                                          # HTML 简历（核心交付物 1）
│   ├── index.html            # 16 KB · 双击即用 · 数据内嵌
│   ├── index-loader.html     # 20 KB · 从 resume.json 加载
│   ├── resume.json           # 7 KB  · 结构化简历数据
│   └── README.md             # 5 KB  · 三种启动方式 + 数据结构说明
│
└── docs/                                                  # 研究 / 简历 / 灵魂地图 等文档（核心交付物 2）
    ├── 研究历程_岗位1岗位6_7_v1.md ~ v4.md                 # 4 个版本的研究历程稿（最终版 v4）
    ├── 简历_对抗性_v1.md / v2.md                           # 抗 AI 误判的简历版本
    ├── 自传_自夸版_v2.md                                  # 自传
    ├── 灵魂地图_双专利改造差_落地版.md
    ├── 灵魂地图_双专利改造差_NELSON_*.md                  # NELSON 改进版
    ├── NELSON_PDF_工程落地把关_GLM5V.md
    ├── CONFIG-配置清单.md
    ├── DESIGN-PREPARTNERS-10.md
    ├── ANCHOR_EYE_搁置总结.md
    ├── CLIENT-交付说明.md
    ├── CONFIDENTIAL-不给甲方.md
    ├── DEMO-DEPLOY.md
    └── PRE-DEVICE-SALE-72H.md
```

## 主要内容

### 1. HTML 简历 (`cv-pro-local/`)

基于开源模板 cv-pro 改造的个人简历网站：

| 文件 | 用途 |
|------|------|
| `cv-pro-local/index.html` | **推荐** · 16 KB · 双击即可在浏览器打开 |
| `cv-pro-local/index-loader.html` | 备选 · 从 `resume.json` 加载（适合经常更新数据） |
| `cv-pro-local/resume.json` | 结构化数据，字段命名沿用 cv-pro 原版 |
| `cv-pro-local/README.md` | 启动方式与字段说明 |

数据字段：`username / header / personalInfo / experience / education / projectsDetailed / projectsRecent / skills / contact / meta`。

### 2. 研究文档 (`docs/`)

按用途分组：

- **岗位 × 岗位研究历程 v1–v4** —— 4 个迭代版本，v4 为最终版（45 KB）
- **简历版本 v1–v2** —— 对抗性版本（防止被简单关键词匹配误解）
- **灵魂地图** —— 双专利改造差 · 落地版 · NELSON 改进版
- **工程落地** —— NELSON PDF · CONFIG 配置 · DEMO · DEPLOY
- **客户/伙伴** —— CLIENT · DESIGN PARTNERS 10 · ANCHOR EYE
- **合规/隐私** —— CONFIDENTIAL · PRE-DEVICE-SALE-72H

### 3. 聊天记录 (`*.transcript.txt` + `*.summary.md`)

今天三段 Cursor 对话的时间线 + 结构化总结：

| 时间 | 主题 | 摘要 |
|------|------|------|
| 10:47–16:18 | 公司与岗位深度阅读 | 19 轮用户提问，309 条助手回复，分析招聘目标公司 + 改造 cv-pro 模板为本地简历 |
| 11:23–18:22 | Notion 内容读取 + GitHub 推送 | 15 轮用户提问，72 条助手回复，尝试通过 Notion API 同步研究材料（最终通过 GitHub API 完成本仓库的推送） |
| 14:49–15:05 | 小红书链接 / ha7ch.com 调研 | 5 轮用户提问，14 条助手回复，研究 ha7ch.com 与 CV Pro 工具 |

## 数据来源

- Cursor agent transcript JSONL 文件保存在本地：  
  `C:\Users\Administrator\.cursor\projects\c-Users-Administrator-Downloads-nonlinear-autobio-main\agent-transcripts\<uuid>\<uuid>.jsonl`
- 仓库目录 `C:\Users\Administrator\Downloads\nonlinear-autobio-main\{cv-pro-local,docs}`
- 解析/推送脚本位于仓库外部的 `_scripts/_parse_transcript.ps1` 和 `_push_to_github.ps1`

## 命名约定

- `*.summary.md` — 结构化总结（首问脉络 + 工具调用统计 + 助手片段）
- `*.transcript.txt` — 完整时间线 transcript（按 `[时间戳] USER/ASSISTANT` 分段）
- 文件前缀 `2026-10-06_<前8位 UUID>_<slug>` 用于在保留原始 Cursor session id 引用链的同时标识日期和主题

## 仓库信息

- **可见性**：私有
- **URL**：https://github.com/18926119195/soul-interview
- **本地 slug**：`soul-interview`（GitHub 不支持中文 slug；"灵魂的面试"作为 display name 在 description 和本 README 中体现）