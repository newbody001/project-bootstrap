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

### Phase 3：创建 CODE_INDEX.md

1. 检查目标项目根目录是否已有 `CODE_INDEX.md`
2. 若不存在：
   - 从 `assets/CODE_INDEX.template.md` 复制到项目根目录
   - **分析项目结构**：读取项目文件列表，识别核心文件
   - **填写索引内容**：在模板中填入：
     - 文件地图表（每个核心文件的职责和关键函数/类）
     - 路由/API 速查表（如有 Web/API 项目）
     - 按功能快速定位表（Agent 查此表即知改哪个文件）
     - 数据流概览
     - 关键注意事项
3. 若已存在：检查是否需要更新（项目结构有变化则更新）

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
