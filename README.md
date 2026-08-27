# NewsFlash-AI · 讯捷AI

一个「一屏尽览」的 AI 新闻助手：在同一个界面内同时展示上百条新闻摘要，自动解释复杂词汇，并支持一键标亮、一键归档到本地笔记文件（.txt / Obsidian 等）。

## ✨ 功能特性

- **海量新闻一屏尽览**：采用 4 列网格布局，可在同一页面展示上百条新闻摘要。
- **复杂词汇自动解释**：AI 自动识别需要解释的复杂词语，以下划线标出，鼠标悬浮即可查看释义。
- **一键标亮**：点击「边框」或「✨」按钮，即可标亮感兴趣的新闻。
- **一键归档到本地**：将标亮的新闻一键写入本地 `.txt` 文件，可直接用于 Obsidian 等笔记软件。
- **多源信息聚合**：
  - 微信公众号「重点 / 非重点」文章摘要
  - GitHub Trending 热榜项目
  - ProductHunt 每日热榜产品
- **一键打开原文**：每条新闻均可一键在新窗口打开原文链接。
- **AI 对话**：内置基于 Langchain 的聊天接口，支持流式输出。

## 🧱 技术栈

| 层 | 技术 |
| --- | --- |
| 前端 | Vue 3 · Vite · Axios |
| 后端 | FastAPI (Python) |
| AI 服务 | Langchain · LLM 提示词引擎 |
| 数据来源 | 微信公众号、GitHub Trending、ProductHunt |

## 📁 项目结构

```
NewsFlash-AI/
├── index.html                  # 入口 HTML（标题：讯捷ai）
├── vite.config.js              # Vite 配置（开发端口 3003）
├── package.json
└── src/
    ├── main.js                 # Vue 应用入口
    ├── App.vue                 # 主界面（核心逻辑）
    ├── style.css               # 全局样式
    ├── services/
    │   └── api.js              # 前端 API 封装（FastAPI / SpringAI）
    ├── public/                 # 静态资源（背景图、favicon）
    ├── python文件/             # Python 后端与爬虫
    │   └── fastapi/
    │       ├── main.py                 # FastAPI 服务入口（端口 8000）
    │       ├── llm总结文章list.py       # LLM 文章总结
    │       ├── prompt.py               # LLM 提示词
    │       ├── 提取需解释的词语.py       # 提取需要解释的复杂词汇
    │       ├── 获取解释词语的位置.py     # 定位词汇在文中的位置
    │       ├── 爬取wx公众号文章信息.py   # 微信公众号文章爬虫
    │       ├── github热榜.py           # GitHub Trending 爬虫
    │       └── 爬取producthunt热榜.py   # ProductHunt 爬虫
    └── news_process_agent/     # 子项目：新闻自动分类整理（Vue 3 + TS）
        ├── backend/
        │   └── main.py         # 分类结果保存到本地 Markdown 的接口
        └── src/views/          # 搜索页 & 处理页
```

## 🚀 快速开始

### 1. 启动前端

```bash
npm install
npm run dev
```

开发服务器默认运行在 `http://localhost:3003`（端口已在 `vite.config.js` 中配置）。

### 2. 启动 Python 后端（FastAPI）

```bash
cd src/python文件/fastapi
uvicorn main:app --reload --port 8000
```

> 后端会执行公众号文章爬取、LLM 总结、GitHub / ProductHunt 热榜抓取，并提供文章摘要、词汇位置、写入文件等接口。

### 3. 启动 AI 对话服务（SpringAI，可选）

聊天功能依赖 `http://localhost:8081` 的 SpringAI 服务。

## 🔌 主要接口

### 数据接口（GET，FastAPI `:8000`）

| 接口 | 说明 |
| --- | --- |
| `/api/summary01` | 重点公众号文章摘要 |
| `/api/summary02` | 非重点公众号文章摘要 |
| `/api/wordposition01` | 需解释词汇在文中的位置 |
| `/api/url01` / `/api/url02` | 重点 / 非重点文章链接 |
| `/api/github` / `/api/github_name` | GitHub 热榜内容 / 项目名 |
| `/api/producthunt` / `/api/producthunt_urls` | ProductHunt 热榜内容 / 链接 |

### 写入接口（POST，FastAPI `:8000`）

| 接口 | 说明 |
| --- | --- |
| `/api/write_texts_to_file` | 写入「公众号文章整理.txt」 |
| `/api/write_texts_to_github_file` | 写入「github项目整理.txt」 |
| `/api/write_texts_to_producthunt_file` | 写入「producthunt产品整理.txt」 |

> 写入的目标路径目前硬编码为 Linux 路径（如 `/home/bill/桌面/...`），如在不同环境使用请修改 `src/python文件/fastapi/main.py`。

## 📖 使用说明

1. 打开前端页面后，自动从后端拉取各栏数据。
2. **重点文章区**：每条摘要下方的「边框」按钮用于标亮选中，「链接」按钮用于打开原文。
3. **复杂词汇**：文中带下划线的文字，鼠标悬浮即可查看 AI 给出的解释。
4. **归档**：标亮感兴趣的条目后，点击对应区域的「写入本地文件」按钮，即可将内容追加写入本地笔记文件。
5. **GitHub / ProductHunt 区域**：点击 ✨ 按钮标亮，再点击「写入本地文件」归档。

## 📦 子项目：news_process_agent

`src/news_process_agent` 是一个独立的新闻自动分类与整理子项目（Vue 3 + TypeScript）：

- 输入多条新闻内容
- 调用 AI 将每条新闻自动归类到 17 个分类之一
- 按分类重排并展示
- 一键将分类后的结果追加写入对应的本地 Markdown 文件

## 📄 License

[MIT](LICENSE)
