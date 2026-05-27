---
name: project-bootstrap
description: >
  Bootstrap a multi-platform "Rules Engine + Code Index" workflow into any project.
  Creates AGENTS.md / CLAUDE.md / CODEBUDDY.md / GEMINI.md (universal canonical rules,
  auto-recognized by Codex, Claude Code, CodeBuddy, Gemini CLI, Qwen Code, Augment, etc.),
  CODE_INDEX.md (project file map), and IDE-specific thin-wrappers
  (Cursor, Windsurf, Copilot, CodeBuddy IDE).
  Use this skill when the user wants to set up AI agent rules for a project,
  bootstrap a new codebase, or add the AGENTS.md / CODE_INDEX.md convention.
---

# Project Bootstrap — 多平台 AI Agent 工作流部署

## 技能用途

将"通用规则引擎 + 项目代码索引 + 多平台适配"这套工作流一次性部署到目标项目。

核心设计：
- **一份规则内容** → 复制为 `AGENTS.md`、`CLAUDE.md`、`CODEBUDDY.md`、`GEMINI.md`
  覆盖 Codex / Claude Code / CodeBuddy / Gemini CLI / Qwen Code / Augment 等平台
- **一份项目索引** → `CODE_INDEX.md`，所有平台共享
- **IDE 薄包装** → Cursor、Windsurf、Copilot、CodeBuddy IDE 读取各自的规则文件，

## 触发条件

- 用户说"初始化项目"、"搭建项目骨架"、"bootstrap 项目"
- 用户说"添加 AI agent 规则"、"设置 agent 工作流"
- 用户说"生成 CODE_INDEX.md"、"创建代码索引"
- 用户提到"AGENTS.md"、"CLAUDE.md"、"多平台 agent 规则"

---

## 支持的平台

| 平台 | 文件名 | 识别方式 |
|------|--------|---------|
| **Codex CLI** | `AGENTS.md` | 自动读取 |
| **Qwen Code** | `AGENTS.md` | 自动读取 |
| **Augment Code** | `AGENTS.md` | 自动读取 |
| **Claude Code** | `CLAUDE.md` | 自动读取 |
| **CodeBuddy** | `CODEBUDDY.md` + `.codebuddy/rules/` | 自动读取 |
| **Gemini CLI** | `GEMINI.md` | 自动读取 |
| **Cursor** | `.cursor/rules/*.mdc` | 自动加载 |
| **Windsurf** | `.windsurfrules` | 自动加载 |
| **GitHub Copilot** | `.github/copilot-instructions.md` | 自动注入 |
| **Aider** | `CONVENTIONS.md` | 可配置 |

## 执行流程

### Phase 1：确认目标项目

确认要初始化的项目根目录。如果是当前工作区则直接使用，否则询问路径。

### Phase 2：部署多平台规则文件

**关键操作**：将 `assets/AGENTS.template.md` 的内容，以不同文件名复制到项目根目录。

检查并部署以下文件（内容完全相同，仅文件名不同）：

| 目标文件 | 说明 |
|---------|------|
| `AGENTS.md` | Codex、Qwen Code、Augment 等读取 |
| `CLAUDE.md` | Claude Code 读取 |
| `CODEBUDDY.md` | CodeBuddy 读取 |
| `GEMINI.md` | Gemini CLI 读取 |

对每个文件：
1. 检查是否已存在
2. 若不存在：从 `assets/AGENTS.template.md` 复制内容写入
3. 若已存在：询问用户是否覆盖或跳过
4. 写完后，将第 2 行 `> 本文件会同时部署为...` 替换为指向其他兄弟文件的说明（即每个平台文件互相知晓彼此的存在）

### Phase 3：创建 CODE_INDEX.md（强化版）

#### Step 3.0 — 检查与决策

检查目标项目根目录是否已有 `CODE_INDEX.md`：
- 若不存在 → 继续 3.1~3.5 完整创建
- 若已存在 → 跳到 3.6 增量更新

#### Step 3.1 — 扫描项目结构（轻量，最多读 2 层）

```
list_files(target_root, depth=2)
```

目标：获取一级目录结构，判断项目类型，**不深入读取文件**。

#### Step 3.2 — 识别项目类型并选择分析策略

根据扫描结果判断项目类型，选取对应策略：

| 检测信号 | 项目类型 | 重点读取路径 |
|---------|---------|------------|
| `app.py` / `manage.py` / `requirements.txt` | **Python / Flask** | 入口文件、`routes/`、`models/`、`templates/` |
| `src/App.vue` / `package.json` 含 vue | **Vue** | `src/views/`、`src/components/`、`src/store/`、`src/router/` |
| `src/App.tsx` / `package.json` 含 react | **React** | `src/pages/`、`src/components/`、`src/hooks/`、`src/services/` |
| `main.go` / `go.mod` | **Go** | 入口文件、`cmd/`、`internal/`、`pkg/` |
| `Cargo.toml` / `src/main.rs` | **Rust** | `src/main.rs`、`src/lib.rs`、`src/` 子模块 |
| `package.json` 无框架特征 | **Node.js 通用** | `index.js`、`src/`、`lib/` |
| 以上都不匹配 | **通用项目** | 按文件名判断：入口文件、配置目录、源码目录 |

#### Step 3.3 — 精准读取核心文件（严格控制 token）

**硬限制：** 全程读取不超过 **8 个文件**，每个文件只读头 **60 行**。

读取顺序：
1. 入口文件（`app.py` / `main.go` / `index.js` 等）—— 读头部了解导入了哪些模块
2. 配置文件（`package.json` / `requirements.txt` / `go.mod` 等）—— 读依赖列表
3. 根据入口文件的 import/require 列表，挑选 3~5 个核心模块文件读取
4. 如有路由/API 定义文件，读取 1 个了解路由结构

**只读**，不写任何文件直到 Step 3.4。

#### Step 3.4 — 写入 CODE_INDEX.md

从 `assets/CODE_INDEX.template.md` 复制模板到项目根目录，然后填入分析结果：

**必填区域（缺一不可）：**

① **文件地图** — 列出 5~12 个核心文件，格式：`| 文件路径 | 一句话职责 | 关键符号 |`

② **按功能快速定位**（核心中的核心） — Agent 查这张表就能找到改哪个文件：
```
| 用户想做什么 | 改哪个文件 | 关键函数/组件 |
|------------|-----------|-------------|
| 改页面布局 | src/views/Home.vue | <template> 区域 |
| 加一个新 API | backend/routes/api.py | def new_endpoint() |
```
**最少 3 条才合格。** 格式必须是表格，不是列表。

③ **关键注意事项** — 列出 2~5 条容易踩坑的点（配置约定、硬编码位置、特殊文件结构等）

**选填区域（有则填）：**

- 路由/API 速查表（Web/API 项目必有）
- 数据流概览

#### Step 3.5 — 验收（自检）

写入后立即自检，逐项核对：

```
[ ] 文件地图 >= 5 行？
[ ] 功能快速定位 >= 3 行（表格格式）？
[ ] 关键注意事项 >= 2 条？
[ ] 每条文件地图都包含"一句话职责" + "关键符号"？
[ ] 没有 "{{PROJECT_NAME}}" 等模板占位符残留？
```

任一项不通过 → 补充后重新验收。

#### Step 3.6 — 增量更新（CODE_INDEX.md 已存在时）

1. 对比当前项目文件列表和 CODE_INDEX.md 中的文件地图
2. 新增文件 → 追加到文件地图和功能定位表
3. 删除文件 → 从索引中移除
4. 路由变化 → 更新路由速查表
5. 更新后执行 3.5 验收

### Phase 4：部署 IDE 规则适配层

根据用户使用的 IDE，选择性部署以下 thin-wrapper：

| IDE | 文件路径 | 来源模板 |
|-----|---------|---------|
| Cursor | `.cursor/rules/project.mdc` | `assets/cursor-rule.mdc` |
| Windsurf | `.windsurfrules` | `assets/windsurf-rule.md` |
| GitHub Copilot | `.github/copilot-instructions.md` | `assets/copilot-instructions.md` |
| CodeBuddy IDE | `.codebuddy/rules/project-readme.md` | `assets/codebuddy-rule.md` |

- Cursor / Windsurf / Copilot：默认全部部署（无副作用）
- CodeBuddy IDE：仅当用户当前使用 CodeBuddy 时部署

每个 thin-wrapper 文件内容：
- 简短 5~8 行
- 指导 Agent 先读根目录规则文件 + CODE_INDEX.md
- 附着平台要求的 frontmatter 元数据（如 Cursor 的 `globs`、`alwaysApply`）

**注意**：如果目标文件已存在且内容已足够，跳过；否则询问后更新。

### Phase 5：验证与总结

部署完成后，列出所有已创建/更新的文件：

```
根目录规则文件（内容相同，多平台各取所需）：
  AGENTS.md      ← Codex / Qwen Code / Augment
  CLAUDE.md      ← Claude Code
  CODEBUDDY.md   ← CodeBuddy
  GEMINI.md      ← Gemini CLI

项目索引：
  CODE_INDEX.md  ← 所有平台共享

IDE 适配层：
  .cursor/rules/project.mdc        ← Cursor
  .windsurfrules                   ← Windsurf
  .github/copilot-instructions.md  ← GitHub Copilot
  .codebuddy/rules/project-readme.md ← CodeBuddy IDE
```

并说明工作流程：

```
Agent 启动 → 自动读取本平台规则文件（AGENTS/CLAUDE/CODEBUDDY/GEMINI.md）
         → 按规则先读 CODE_INDEX.md
         → 从索引定位目标文件
         → 精准读写 → 验证
```

---

## 资产清单

| 文件 | 用途 |
|------|------|
| `assets/AGENTS.template.md` | 通用规则内容（复制为 AGENTS/CLAUDE/CODEBUDDY/GEMINI.md） |
| `assets/CODE_INDEX.template.md` | 项目代码索引模板 |
| `assets/cursor-rule.mdc` | Cursor IDE 规则（含 MDC frontmatter） |
| `assets/codebuddy-rule.md` | CodeBuddy IDE 规则（含 YAML frontmatter） |
| `assets/windsurf-rule.md` | Windsurf IDE 规则 |
| `assets/copilot-instructions.md` | GitHub Copilot 指令 |
