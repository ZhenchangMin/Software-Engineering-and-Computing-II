# 00-06 项目级 VibeCoding

> Stitch · AI Studio · 通义灵码 · Claude Code · Expo Go · MCP
> "Vibe Coding：用自然语言驱动，从原型到上线的全栈开发新范式"

## 什么是 VibeCoding？

> "You fully give in to the vibes, embrace exponentials, and forget that the code even exists." —— Andrej Karpathy, 2025

核心思想：不再逐行手写代码，而是**用自然语言描述意图，让 AI 完成实现**。

| 传统开发 | VibeCoding |
|------|------|
| 需要精通语言语法 | 只需清晰描述需求 |
| 手动搭建项目结构 | AI 自动生成脚手架 |
| 前后端分别学习 | 统一用对话驱动 |
| 耗时数天搭原型 | 数小时跑通全栈 |

**工具链分工**：界面原型（Google Stitch）→ Web 前端（Google AI Studio）→ Web 后端（Claude Code）→ 移动端 App（通义灵码 + Expo）→ 真机调试（Expo Go）→ Web 自动调试（MCP chrome-devtools）。

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

## PART 4：通义灵码开发移动端 App

**通义灵码**（VS Code 插件 / Lingma IDE）：与 AI Studio + Claude Code 并行的另一条路线，适合已在用阿里云生态（DashScope API / 通义系列）的团队，全程在 VS Code 内完成。

| 技术栈 | 特点 | 适合场景 |
|------|------|------|
| Expo (React Native) | JS/TS，热重载，Expo Go 预览 | 快速原型，跨平台 |
| Flutter | Dart，高性能，UI 统一 | UI 精度要求高 |
| 原生 Swift/Kotlin | 最佳性能，学习成本高 | 生产级 App |

> 推荐：**Expo (React Native) + 通义灵码**——上手最快，与 Web 前端知识复用度最高。

**流程**：`npx create-expo-app@latest`（命名 demo-app-frontend）→ 灵码对话生成界面（灵码会**先自动读取项目结构**再生成代码）→ 生成后端（demo-app-backend, Node.js + Express）→ 用 RTK Query 做前后端通信 → 接入通义大模型（qwen-plus，`DASHSCOPE_API_KEY` 配在 .env，通过 OpenAI 兼容接口调用）。

### Expo Go 真机调试

安装 Expo Go App → `npm start` 输出二维码 → 手机扫码实时预览。**热重载**：改代码保存 → Expo Go 自动刷新，无需重新编译。

| 调试场景 | 操作 |
|------|------|
| 界面布局错误 | Shake 手机 → Inspector 查看样式 |
| 网络请求失败 | Expo Dev Tools → Network 面板 |
| JS 报错 | 红屏报错 → 直接截图复制给灵码修复 |

> ⚠ **真机调试时后端地址不能用 localhost**，需改为电脑的局域网 IP（如 `192.168.1.x:3000`）。常见错误："Network request failed" → localhost 在真机不可用；路径别名 `@` 未配置 → 修复 tsconfig.json 的 paths。

## PART 5：MCP — chrome-devtools 自动调试 Web 端

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

## PART 6：前后端集成与运行

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

> ⚠ 警惕：AI 生成的代码可能包含安全漏洞（SQL 注入、未校验输入）。**上线前必须做安全审查！**

## 六讲总结：AI4Coding + VibeCoding 完整图景

```
00-01 AI 辅助编程入门    → 工具生态·六大能力·提示词四要素
00-02 开发工具链与 Git   → DevOps·Git 四区域·Vercel 部署
00-03 AI 编程进阶与 Agent → 项目级技巧·代码质量·SE 未来
00-04 前端开发基础       → Web 演进·Vue.js·Node.js
00-05 Java Web 开发      → Spring Boot·MyBatis·Spring Security
00-06 项目级 VibeCoding  → Stitch·AI Studio·灵码·Claude Code·Expo Go·MCP
```

> **核心洞察：AI 工具改变的是速度和交互方式，不变的是需求分析、架构设计与工程纪律。**
