# 00-06 项目级 VibeCoding

> Stitch · AI Studio · 通义灵码 · Claude Code · Expo Go · MCP
> "Vibe Coding：用自然语言驱动，从原型到上线的全栈开发新范式"

## 什么是 VibeCoding？

> "You fully give in to the vibes, embrace exponentials, and forget that the code even exists." —— Andrej Karpathy, 2025

核心思想：不再逐行手写代码，而是**用自然语言描述意图，让 AI 完成实现**。

## PART 1：用 Stitch 设计界面原型

**Google Stitch**（stitch.withgoogle.com）：AI 驱动界面原型工具。输入自然语言描述，自动生成 App/Web 界面原型，支持双端预览，可导出组件代码（HTML/React），与 Material Design 整合。

**操作流程**：① 新建项目 → ② 描述应用（明确**页面数量、核心功能、用户场景**）→ ③ 自动生成（10–30 秒）→ ④ 点击组件继续对话修改 → ⑤ 导出截图/代码。

| 维度 | Figma | Stitch |
|------|------|------|
| 学习成本 | 中等（需学组件系统） | 低（自然语言驱动） |
| 生成速度 | 手动拖拽，较慢 | AI 自动，秒级 |
| 适用阶段 | 精细设计稿 | 快速原型验证 |

> VibeCoding 场景下，Stitch 用于快速对齐需求，Figma 用于最终交付物——两者互补。

## PART 2：用 AI Studio 生成前端代码

**Google AI Studio**（aistudio.google.com）：基于 Gemini 的 Web 端 AI 开发平台。支持**长上下文（100 万+ tokens）**，可上传整个原型截图 + 需求文档，浏览器内预览代码，通过 API Key 接入 Gemini。

**前端初始化**（Vite + React）：
```bash
npm create vite@latest my-app-frontend -- --template react
cd my-app-frontend && npm install
npm install axios react-router-dom tailwindcss
```

**生成流程**：上传 Stitch 原型截图 → 描述技术栈（React + Tailwind）→ Gemini 分析布局 → 生成组件（`KanbanBoard.jsx` 等）→ 自动处理样式 → 预留 API 接口占位。

| 目标 | 提示词模板 |
|------|------|
| 生成新页面 | "基于已有 Layout.jsx 结构，生成 [页面名] 组件" |
| 接入真实 API | "将 mock 数据替换为调用 GET /api/tasks 的真实请求，处理 loading 和 error 状态" |
| 添加交互 | "点击卡片弹出侧边栏，使用 Framer Motion 添加滑入动画" |
| 代码审查 | "检查此组件是否有内存泄漏或 React key 警告" |

> AI Studio 的长上下文优势：可一次性粘贴多个文件代码，让 AI 理解整体结构后再修改。

## PART 3：用 Claude Code 生成后端

**Claude Code**：Anthropic 的终端 AI 编程 Agent。在本地终端运行、直接读写项目文件、理解整个代码库上下文、自主执行命令（npm/git/测试）、多轮对话持续完善。

```bash
npm install -g @anthropic-ai/claude-code
cd my-app-backend && claude
```

**后端生成**：描述需求（Node.js + Express + SQLite）→ Claude Code 自动 `mkdir`、`npm init`、安装依赖、生成 `src/index.js` `src/routes/` `src/db/` 完整结构 → 实现 CRUD API、建表、输入校验、seed 数据。

**接入 AI 能力**：新增 `POST /api/ai/suggest`，从环境变量 `GEMINI_API_KEY` 读取密钥，调用 Gemini 返回任务分解步骤。

## PART 4：MCP — chrome-devtools 自动调试 Web 端

**MCP（Model Context Protocol）**：AI 与工具之间的标准通信协议，由 Anthropic 提出，已成事实标准。允许 AI（Claude Code / 灵码）直接调用外部工具，每个 MCP Server 赋予 AI 一种新能力。

> 过去：AI 看不到浏览器里发生了什么，只能根据文字描述猜测问题。**现在：AI 可直接读取控制台报错、网络请求、DOM 结构。**

常用 MCP Server：chrome-devtools（读浏览器 Console/Network/DOM）、github（操作 Issue/PR）、vercel（一键部署）、mcp-server-sqlite（查数据库）、mcp-server-chart（生成图表）。

**配置 chrome-devtools**：① 在 `settings.json` 的 `mcpServers` 中声明 → ② 以远程调试模式启动 Chrome：
```bash
# Windows
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222
```

> chrome-devtools "连接失败" 通常因为 Chrome 未以调试端口启动。

### 自动调试流程

直接对 Claude Code 描述问题 → 它通过 MCP 自动：① 连接 Chrome DevTools(CDP) → ② 读 Console 检查 JS 错误 → ③ 读 Network 检查 API 请求 → ④ 检查 DOM 结构 → ⑤ 注入调试代码监听事件 → ⑥ 定位根因→修改源码→刷新。**全程无需手动打开 DevTools。**

> 实战示例：登录后页面空白 → Console 发现 `Cannot read properties of undefined (reading 'map')` at TaskList.jsx:42 → Network 发现 `GET /api/tasks` 返回 `{ "data": null }` → 自动修复 `const tasks = data?.tasks ?? [];` 加空值保护。
> **MCP 的价值：AI 从"盲人摸象"变为"亲眼观察"，调试效率提升 5–10x。**

## PART 5：前后端集成与运行

**前端用 Axios 对接**：统一封装 axios 实例（baseURL、timeout、错误/loading 处理）。

**启动联调**：终端 1 启动后端（`npm run dev` → :3000），终端 2 启动前端（`npm run dev` → :5173）。

| 常见问题 | 对 Claude Code 说 |
|------|------|
| 跨域报错(CORS) | "后端开启 CORS，允许 localhost:5173 访问" |
| 接口 404 | "检查路由注册是否正确，打印所有已注册路由" |
| 数据格式不匹配 | "前端字段是 due_date，改为 dueDate 驼峰命名" |
| SQLite 并发写入 | "开启 WAL 模式解决并发写入" |

## VibeCoding 的软件工程思考

**改变了什么**：
- 编程门槛从"语法精通"降为"需求表达能力"。
- 工程师价值从"写代码"转向"定义问题 + 审查 AI 输出"。
- 原型验证速度提升 10x，但**最终质量仍取决于人的判断**。

**没有改变什么**：
- 需求分析、系统设计、架构决策——仍是人的责任。
- 安全性、性能、可维护性——AI 生成的代码需人工审查。
- 测试与部署——自动化可辅助，但不能省略。