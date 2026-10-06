# 灵魂的面试 — Soul's Interview

> 一份关于「求职 / 研究 / 自我探索」的真实对话档案。

本仓库收录了 **2026-10-06 (Tuesday)** 三个独立对话的完整记录与结构化总结：

| 时间 | 主题 | 用户提问 | 助手回复 | 工具调用 | 关键内容 |
|---|---|---|---|---|---|
| 10:47 → 16:18 | 公司与岗位深度阅读 | 19 | 309 | 364 | 用户上传岗位/公司截图，分析招聘目标公司的业务、岗位画像，并结合项目库做匹配 |
| 11:23 → 18:22 | Notion 内容读取与 GitHub 推送 | 15 | 72 | 77 | 用户希望通过 Notion API 把研究材料全部读出来；推进中遇到 PowerShell/JSON/编码问题 |
| 14:49 → 15:05 | 小红书链接读取与 ha7ch.com 调研 | 5 | 14 | 13 | 助手尝试读取受登录墙保护的小红书链接，转而调研 ha7ch.com 与 CV Pro |

> 注：上面数字从 `_out/*.summary.md` 的统计字段直接读取。

## 目录结构

```
.
├── README.md
├── 2026-10-06_e63b63b1_xiaohongshu-link-reading.summary.md       # 小红书 / ha7ch.com 对话总结
├── 2026-10-06_e63b63b1_xiaohongshu-link-reading.transcript.txt  # 完整 transcript
├── 2026-10-06_50d8d604_notion-content-reading-and-github-push.summary.md
├── 2026-10-06_50d8d604_notion-content-reading-and-github-push.transcript.txt
├── 2026-10-06_bd5c9400_company-and-role-deep-read.summary.md
└── 2026-10-06_bd5c9400_company-and-role-deep-read.transcript.txt
```

## 命名约定

- `*.summary.md` — 结构化总结（首问脉络 + 工具调用统计 + 助手片段）
- `*.transcript.txt` — 完整时间线 transcript（按 `[时间戳] USER/ASSISTANT` 分段，包含 tool_call / tool_result 代码块）
- 文件前缀 `2026-10-06_<前8位 UUID>_<slug>` 用于在保留原始 Cursor session id 引用链的同时标识日期和主题

## 引用说明

- 原始 Cursor agent transcript JSONL 文件保存在本地：  
  `C:\Users\Administrator\.cursor\projects\c-Users-Administrator-Downloads-nonlinear-autobio-main\agent-transcripts\<uuid>\<uuid>.jsonl`
- 解析脚本位于仓库外部的 `_scripts/_parse_transcript.ps1`
- 本仓库为 **私有** 仓库，仅限授权用户访问

## 数据来源说明

对话涵盖求职准备的两个核心动作：
1. **理解目标** — 通过截图 + 搜索 + 抓取，分析招聘方的需求和潜在价值观
2. **盘点自我** — 把自己的 Notion 研究笔记 / 项目仓库反向结构化为可复述的研究历程