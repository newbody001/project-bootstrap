---
description: 项目AI协作规则 → 读取规则文件 + CODE_INDEX.md
alwaysApply: true
enabled: true
---

# 项目协作规则

本项目的 AI Agent 行为由项目根目录的规则文件（`AGENTS.md` / `CLAUDE.md` / `CODEBUDDY.md` / `GEMINI.md`）
与 `CODE_INDEX.md`（项目代码索引）联合驱动。

## 开始任何任务前

1. 读取根目录下的平台规则文件 — 了解工具使用纪律和工作流程
2. 读取 `CODE_INDEX.md` — 定位目标文件所在位置
3. 按索引精准读取和修改代码，只加载相关文件

## 核心铁律

- **禁止跳过 CODE_INDEX.md 直接扫描项目**
- **禁止重写大文件，使用 replace_in_file**
