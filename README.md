# 🚀 Project Bootstrap

[English](./README_EN.md) | 简体中文

> **一份引擎，一份燃料，十平台通用。** 将 AI Agent 协作规范一键部署到任意项目，让所有主流 AI 编程助手自动遵守统一的行为纪律。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platforms](https://img.shields.io/badge/platforms-10+-blue)]()

---

## 🤔 解决的问题

用 AI 编程助手写代码时，你是否遇到过这些情况？

- 😤 Agent 一上来就全项目扫描，浪费大量 token
- 😤 Agent 用 `write_to_file` 覆盖你精心修改的文件
- 😤 Agent 在一个文件里反复读改，每次都从头读到尾
- 😤 换一个 AI 工具，之前的规则全白费，又要重新设置

**Project Bootstrap** 一次性解决这些问题。它为你的项目建立一套 **跨平台通用规则引擎**，让所有 AI 编程助手自动遵守你定义的工作纪律。

---

## ✨ 核心设计

```
┌──────────────────────────────────────────┐
│      AGENTS.md  (通用规则引擎)             │
│                                          │
│  "先读索引 → 精准定位 → 小步修改 → 验证"    │
│                                          │
│  一次编写，复制为 4 个文件名部署到项目根目录  │
└──────────────────┬───────────────────────┘
                   │
         ┌─────────┼─────────┬──────────────┐
         ▼         ▼         ▼              ▼
    AGENTS.md  CLAUDE.md CODEBUDDY.md  GEMINI.md
    ↑          ↑         ↑             ↑
  Codex     Claude    CodeBuddy     Gemini
  Qwen      Code                    CLI
  Augment
                   │
                   ▼
         ┌─────────────────────┐
         │   CODE_INDEX.md      │
         │   (项目专属索引)      │
         │                     │
         │ "改登录 → auth.py   │
         │  改样式 → main.css  │
         │  加API → routes.py" │
         └─────────────────────┘
```

| 概念 | 文件 | 作用 |
|------|------|------|
| **引擎** 🧠 | `AGENTS.md` | 通用行为规范，跨项目复用。定义 Agent 的工作流程、工具纪律、禁止事项 |
| **燃料** ⛽ | `CODE_INDEX.md` | 项目专属地图。记录每个文件的职责，Agent 查表即知改哪个文件 |
| **适配器** 🔌 | IDE 薄包装层 | 让 Cursor / Windsurf / Copilot 等 IDE 也自动加载同一套规则 |

---

## 🎯 支持的平台（10+）

| 平台 | 机制 | 接入方式 |
|------|------|---------|
| **Codex CLI** | 自动读取 `AGENTS.md` | 零配置 |
| **Claude Code** | 自动读取 `CLAUDE.md` | 零配置 |
| **CodeBuddy** | 自动读取 `CODEBUDDY.md` | 零配置 |
| **Gemini CLI** | 自动读取 `GEMINI.md` | 零配置 |
| **Qwen Code** | 自动读取 `AGENTS.md` | 零配置 |
| **Augment Code** | 自动读取 `AGENTS.md` | 零配置 |
| **Cursor** | `.cursor/rules/*.mdc` | 自动加载 |
| **Windsurf** | `.windsurfrules` | 自动加载 |
| **GitHub Copilot** | `.github/copilot-instructions.md` | 自动注入 |
| **Aider** | `CONVENTIONS.md` | 可配置 |

> 🎉 同一套规则，所有平台自动生效。换工具不用换规则！

---

## 🧩 Agent 强制执行的工作流

部署后，所有 AI Agent 将自动遵守以下流程：

```
Step 0: 读取本平台规则文件
    ↓
Step 1: 读取 CODE_INDEX.md（严禁跳过）
    ↓
Step 2: 从索引定位目标文件，只读相关文件
    ↓
Step 3: 使用 replace_in_file 精准修改（10-50 行/次）
    ↓
Step 4: 验证修改（lint / import / 逻辑合理性）
```

### 关键纪律

| ✅ 必须 | ❌ 禁止 |
|---------|---------|
| 先读索引再改代码 | 跳过索引直接全项目扫描 |
| `replace_in_file` 精准修改 | 对 1000+ 行文件全文重写 |
| 只读任务相关文件 | 递归遍历目录读取无关文件 |
| 修改后立即验证 | 连续 `replace` 同一文件 3+ 次不重读 |

---

## 🚀 快速开始

### 作为 WorkBuddy Skill 使用（推荐）

在 WorkBuddy 中安装此 Skill 后，对任意项目说：

```
初始化这个项目
```

Skill 会自动：
1. 部署 `AGENTS.md` / `CLAUDE.md` / `CODEBUDDY.md` / `GEMINI.md`
2. 分析项目结构，生成专属 `CODE_INDEX.md`
3. 部署 Cursor / Windsurf / Copilot / CodeBuddy IDE 适配规则
4. 输出部署总结

### 手动部署

```bash
# 1. 复制通用规则（按目标平台命名）
cp assets/AGENTS.template.md /path/to/project/AGENTS.md    # Codex / Qwen / Augment
cp assets/AGENTS.template.md /path/to/project/CLAUDE.md    # Claude Code
cp assets/AGENTS.template.md /path/to/project/CODEBUDDY.md # CodeBuddy
cp assets/AGENTS.template.md /path/to/project/GEMINI.md    # Gemini CLI

# 2. 创建项目专属索引
cp assets/CODE_INDEX.template.md /path/to/project/CODE_INDEX.md
# 然后手动填写项目文件地图

# 3. 部署 IDE 适配层（可选）
cp assets/cursor-rule.mdc /path/to/project/.cursor/rules/project.mdc
cp assets/windsurf-rule.md /path/to/project/.windsurfrules
cp assets/copilot-instructions.md /path/to/project/.github/copilot-instructions.md
```

---

## 📁 项目结构

```
project-bootstrap/
├── SKILL.md                      # WorkBuddy Skill 定义（入口）
└── assets/
    ├── AGENTS.template.md        # 通用规则引擎（核心）
    ├── CODE_INDEX.template.md    # 项目索引模板
    ├── cursor-rule.mdc           # Cursor IDE 适配层
    ├── codebuddy-rule.md         # CodeBuddy IDE 适配层
    ├── windsurf-rule.md          # Windsurf IDE 适配层
    └── copilot-instructions.md   # GitHub Copilot 适配层
```

---

## 💡 设计哲学

> **AGENTS.md 是引擎，CODE_INDEX.md 是燃料。换项目只换燃料，引擎不变。**

- **一份规则，四处部署**：同一个 `AGENTS.template.md` 的内容，复制为 4 个文件名，适配不同平台的自动加载机制
- **通用与专属分离**：行为规范跨项目复用，项目信息单独维护在 `CODE_INDEX.md`
- **零配置接入**：所有主流平台都能自动识别并加载规则文件，不需要任何额外配置
- **薄包装模式**：IDE 适配层只有 5-22 行代码，仅做"帮 Agent 找到规则文件"这一件事

---

## 🛠️ 自定义

`assets/AGENTS.template.md` 中的规则是通用起点，你可以根据团队习惯修改：

- **调整工具纪律**：修改 Section 2 中的 DO/DON'T 规则
- **添加语言规范**：在 Section 3 中增加 Go / Rust / TypeScript 等语言特定约束
- **强化禁止项**：在 Section 5 中添加团队特有的红线规则

修改后重新部署到项目即可，所有平台同步生效。

---

## 📄 License

MIT © 2026

---

## ⭐ Star History

如果这个项目对你有帮助，请给个 Star ⭐ 支持一下！
