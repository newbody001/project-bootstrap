# 🚀 Project Bootstrap

[简体中文](./README.md) | English

> **One engine. One index. Ten platforms.** A universal rules framework that tames every AI coding assistant — so they follow your conventions, not their own instincts.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platforms](https://img.shields.io/badge/platforms-10+-blue)]()

---

## 🤔 The Problem

AI coding assistants are powerful — and chaotic. Sound familiar?

- 😤 The agent scans your entire repo on every prompt, burning tokens for nothing
- 😤 It overwrites carefully crafted files with `write_to_file`
- 😤 It reads the same 1000-line file top-to-bottom three times in a row
- 😤 You switch from Claude Code to Codex — and all your rules vanish

**Every platform speaks its own dialect. Project Bootstrap makes them all understand the same language.**

---

## ✨ How It Works

```
┌──────────────────────────────────────────┐
│          AGENTS.template.md               │
│        (Universal Rules Engine)           │
│                                          │
│  "Read index → pinpoint file →           │
│   surgical edit → validate"              │
│                                          │
│  Written once. Deployed as 4 filenames.   │
└──────────────────┬───────────────────────┘
                   │
         ┌─────────┼─────────┬──────────────┐
         ▼         ▼         ▼              ▼
    AGENTS.md  CLAUDE.md CODEBUDDY.md  GEMINI.md
       ▲           ▲          ▲           ▲
    Codex      Claude     CodeBuddy    Gemini
    Qwen        Code         │          CLI
    Augment                  │
                             ▼
                  ┌──────────────────────┐
                  │    CODE_INDEX.md      │
                  │  (Project-specific)   │
                  │                      │
                  │  "Change login?      │
                  │   → auth.py"         │
                  │  "Fix styles?        │
                  │   → main.css"        │
                  └──────────────────────┘
```

| Concept | File | Purpose |
|---------|------|---------|
| **Engine** 🧠 | `AGENTS.md` | Universal behavior rules. Defines workflow, tool discipline, and constraints — works across any project. |
| **Fuel** ⛽ | `CODE_INDEX.md` | Project-specific map. Tells the agent exactly which file handles what — no more guessing. |
| **Adapters** 🔌 | Thin IDE wrappers | 5-22 line shims that make Cursor, Windsurf, and Copilot auto-load the same rules. |

---

## 🎯 Supported Platforms (10+)

| Platform | Auto-Loads | Setup Required |
|----------|-----------|----------------|
| **Codex CLI** | `AGENTS.md` | Zero |
| **Claude Code** | `CLAUDE.md` | Zero |
| **CodeBuddy** | `CODEBUDDY.md` | Zero |
| **Gemini CLI** | `GEMINI.md` | Zero |
| **Qwen Code** | `AGENTS.md` | Zero |
| **Augment Code** | `AGENTS.md` | Zero |
| **Cursor** | `.cursor/rules/*.mdc` | Auto-loaded |
| **Windsurf** | `.windsurfrules` | Auto-loaded |
| **GitHub Copilot** | `.github/copilot-instructions.md` | Auto-injected |
| **Aider** | `CONVENTIONS.md` | Configurable |

> 🎉 Same rules, every platform. Switch tools without rewriting anything.

---

## 🧩 The Enforced Agent Workflow

Once deployed, every AI agent follows this exact sequence:

```
Step 0: Load the platform rule file (AGENTS.md / CLAUDE.md / ...)
    ↓
Step 1: Read CODE_INDEX.md — NEVER skip this
    ↓
Step 2: Look up the target file in the index. Read only relevant files.
    ↓
Step 3: Edit surgically with replace_in_file (10-50 lines per edit)
    ↓
Step 4: Validate — check imports, linter, logic consistency
```

### The Rules of Engagement

| ✅ Must Do | ❌ Must Never Do |
|------------|------------------|
| Read the index before touching code | Scan the entire repo blindly |
| Use `replace_in_file` for surgical edits | Rewrite 1000-line files in full |
| Read only task-relevant files | Recursively walk directories reading everything |
| Validate immediately after each change | Chain 3+ edits to the same file without re-reading |

---

## 🚀 Quick Start

### As a WorkBuddy Skill (Recommended)

Install this Skill in WorkBuddy, then in any project just say:

```
Initialize this project
```

The Skill automatically:
1. Deploys `AGENTS.md` / `CLAUDE.md` / `CODEBUDDY.md` / `GEMINI.md`
2. Analyzes your project structure, generates a tailored `CODE_INDEX.md`
3. Deploys Cursor / Windsurf / Copilot / CodeBuddy IDE adapters
4. Prints a deployment summary

### Manual Setup

```bash
# 1. Copy universal rules (one per target platform)
cp assets/AGENTS.template.md /target/project/AGENTS.md    # Codex / Qwen / Augment
cp assets/AGENTS.template.md /target/project/CLAUDE.md    # Claude Code
cp assets/AGENTS.template.md /target/project/CODEBUDDY.md # CodeBuddy
cp assets/AGENTS.template.md /target/project/GEMINI.md    # Gemini CLI

# 2. Create project-specific index
cp assets/CODE_INDEX.template.md /target/project/CODE_INDEX.md
# Then fill in your project's file map

# 3. Deploy IDE adapters (optional)
cp assets/cursor-rule.mdc /target/project/.cursor/rules/project.mdc
cp assets/windsurf-rule.md /target/project/.windsurfrules
cp assets/copilot-instructions.md /target/project/.github/copilot-instructions.md
```

---

## 📁 Project Structure

```
project-bootstrap/
├── SKILL.md                      # WorkBuddy Skill definition (entry point)
├── README.md                     # Full documentation (Chinese)
├── README_EN.md                  # This file (English)
└── assets/
    ├── AGENTS.template.md        # Universal rules engine (the core)
    ├── CODE_INDEX.template.md    # Project index template
    ├── cursor-rule.mdc           # Cursor IDE adapter
    ├── codebuddy-rule.md         # CodeBuddy IDE adapter
    ├── windsurf-rule.md          # Windsurf IDE adapter
    └── copilot-instructions.md   # GitHub Copilot adapter
```

---

## 💡 Design Philosophy

> **AGENTS.md is the engine. CODE_INDEX.md is the fuel. Swap projects — change the fuel, keep the engine.**

- **Write once, deploy everywhere.** Same `AGENTS.template.md` content, copied under 4 filenames. Each platform's auto-load mechanism picks it up natively.
- **Universal vs. project-specific, cleanly separated.** Behavioral conventions are reusable across projects. Project details live exclusively in `CODE_INDEX.md`.
- **Zero-config onboarding.** Every supported platform detects and loads rules files automatically — no settings, no plugins, no CLI flags.
- **Thin adapter pattern.** Each IDE wrapper is 5-22 lines. It does exactly one thing: point the agent to the rules file.

---

## 🛠️ Customization

The rules in `assets/AGENTS.template.md` are a starting point. Adjust for your team:

- **Tool discipline**: Modify the DO/DON'T rules in Section 2
- **Language constraints**: Add Go / Rust / TypeScript-specific conventions in Section 3
- **Team red lines**: Hard rules your team never breaks go in Section 5

Deploy after editing — all platforms pick up the changes simultaneously.

---

## 📄 License

MIT © 2026

---

## ⭐ Star History

If this project saves you from agent-induced chaos, a ⭐ would mean the world!
