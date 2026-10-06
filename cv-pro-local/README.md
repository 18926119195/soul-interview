# CV Pro 本地版 — 动态简历

受 [HA7CH/cv-pro](https://github.com/HA7CH/cv-pro) 启发的**零依赖、可双击运行的本地动态简历方案**。

复刻了原版的核心设计理念：

| 原版特性                          | 本地版实现                                |
| --------------------------------- | ----------------------------------------- |
| 单页简历 + 打印优化（PDF 单页）   | ✅ `@media print` 紧凑排版                |
| 中英文字体智能切换                | ✅ 检测 CJK 自动切 Noto Serif SC          |
| 人类视图 / Agent JSON 双视图      | ✅ 顶部 `for human / for agent` 切换      |
| 一键 TXT 下载 / 一键 PDF 导出     | ✅ 工具栏按钮                             |
| JSON 高亮（key/str/bool/num）    | ✅ 同色系语法高亮                         |
| URL 智能展示（去除 https://）     | ✅ `prettyUrl()`                          |
| 日期 `YYYY-MM` → `Sep 2026`       | ✅ 含 `Expected` 未来月份判定             |
| 学历去重（degree 已含 major 时）  | ✅ `dedupeDegree()`                       |

---

## 📁 文件清单

```
cv-pro-local/
├── index.html          # 主入口（数据内嵌，直接双击打开即可）
├── index-loader.html   # 备选入口（从外部 resume.json 加载，需本地 HTTP）
├── resume.json         # 简历数据（用 index-loader.html 时编辑这个）
└── README.md           # 本文件
```

---

## 🚀 三种使用方式

### 方式 1：直接双击 `index.html`（最简单）

✅ **零配置、零依赖**，双击文件即可在本地浏览器中查看。

> 数据已内嵌在 `<script>` 标签的 `RESUME_DATA` 对象里。修改时编辑 `index.html` 内 `const RESUME_DATA = { ... }`。

### 方式 2：用本地 HTTP 服务（推荐）

把简历数据放在独立的 `resume.json`，方便多端同步或版本管理。

```powershell
# 在 cv-pro-local 目录下任选其一
python -m http.server 8000        # Python
npx serve .                       # Node.js
```

打开 `http://localhost:8000/index-loader.html`。

> ⚠️ 不能直接 `file://` 打开 `index-loader.html`，浏览器会拒绝 fetch 本地文件。

### 方式 3：部署到任意静态托管

把 `index.html`、`index-loader.html`、`resume.json` 三个文件扔到：

- **Vercel / Netlify / Cloudflare Pages**：直接拖进去即可
- **GitHub Pages**：推送到 repo，目录选 `cv-pro-local/`
- 部署 `index-loader.html` 为入口即可，修改 `resume.json` 即可更新简历

---

## ✏️ 数据结构（完全兼容 cv-pro schema）

参考 `resume.json` 的字段：

```js
{
  username: "your-handle",       // 影响 TXT 下载文件名
  header: { name: "姓名" },
  personalInfo: {
    email: "必填",
    phone:   "可选",
    location: "可选",
    pronouns: "可选",
    mbti: "可选",
    birthday: "可选（公开页面会自动隐藏生日，参考原版隐私设计）"
  },
  experience: [
    {
      company: "公司",
      role: "职位",
      startDate: "2023-04",     // YYYY-MM 或 YYYY-MM-DD
      endDate:  "",             // 留空 = 至今
      bullets: ["成就 1", "成就 2"],
      tags: ["React"]
    }
  ],
  education: [
    {
      school: "学校",
      major: "专业",
      degree: "Bachelor / Master / PhD",
      startDate: "2014-09",
      endDate:   "2018-06"
    }
  ],
  projectsDetailed: [   /* 详细项目 */ ],
  projectsRecent:    [   /* 一句话近期项目 */ ],
  skills: [
    { name: "Languages", items: ["TypeScript", "Python"] }
  ],
  contact: [
    { label: "GitHub", url: "https://github.com/xxx" }
  ],
  meta: { updatedAt: "2026-10-06T15:00:00.000Z" }
}
```

---

## 🎨 可定制项

所有样式都在 `<style>` 标签顶部 `:root` 变量里：

```css
--ink-900    /* 主文字色 */
--max-width  /* 简历宽度，默认 48rem (max-w-3xl) */
--font-en    /* 英文字体 */
--font-cn    /* 中文字体 */
```

字体本身通过 Google Fonts CDN 加载（`Montserrat` + `Noto Serif SC`），离线场景可改为本地字体文件。

---

## 🔍 与原版的差异

| 维度             | 原版 cv-pro                                  | 本地版                              |
| ---------------- | -------------------------------------------- | ----------------------------------- |
| 部署             | Next.js + Supabase + Vercel                  | 纯 HTML，零服务                      |
| 多租户           | ✅ `cv.ha7ch.com/{handle}` 多用户托管       | ❌ 单文件单用户                     |
| 后端 / DB        | ✅ Supabase                                  | ❌ 无                                |
| MCP / CLI        | ✅ `get_resume` / `update_resume` 等工具    | ❌ 无（agent 可直接读 `resume.json`）|
| AI 编辑          | ✅ token 鉴权 + CLI                           | ✏️ 用户直接改 JSON                  |
| 隐私脱敏         | ✅ 公开页自动隐藏 phone / 微信 / 生日        | ⚠️ 由用户自行决定（当前完整展示）   |
| schema 校验      | ✅ Zod                                       | ✅ 轻量内联校验（必填项）            |

如果你只是想要一份"活"的个人简历，**本地版足够**；如果你要构建一个公开的多用户简历托管服务，参考原版仓库 README 部署完整版。