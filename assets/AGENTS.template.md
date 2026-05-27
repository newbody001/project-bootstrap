# AI Agent 行为协议（Action Protocol）

> **这不是代码规范。这是行为指令。**
>
> 本文档的目标不是告诉你"代码应该怎么写"，而是告诉你**收到任务后第一步做什么、第二步做什么、什么绝对不能做**。
>
> 本文件会同时部署为 `AGENTS.md`、`CLAUDE.md`、`CODEBUDDY.md`、`GEMINI.md`。

---

## ⛔ 行为覆盖声明（最高优先级）

**你的默认行为模式是：收到任务 → 读目标文件 → 修改。本条声明覆盖此模式。**

在此项目中，你的执行路径被替换为：

```
收到任务 → 读 CODE_INDEX.md → 任务分级(T1/T2/T3) → 按级别执行 → 验证 → 维护索引
```

在完成 `读 CODE_INDEX.md` 和 `任务分级` 之前，**不要读取任何源代码文件**。

修改完成后，**必须自检 CODE_INDEX.md 是否需要更新**。

---

## 0. 任务分级 —— 收到任务后的第一件事

**读完 CODE_INDEX.md 后，不要立刻开始改代码。先判断任务属于哪个级别。**

### T1 — 局部修改（默认级别）

| 特征 | 示例 |
|------|------|
| 改一个数值/常量 | "把 AP 上限从 4 改成 5" |
| 改一个函数的内部逻辑 | "get_score() 里加一个判断条件" |
| 加一个简单路由 | "加个 /api/status 返回 uptime" |
| 修一个明确 bug | "第 42 行除以零了，加个保护" |
| 涉及文件 ≤ 3 个 | |

**策略：精准片段模式** — 从 CODE_INDEX.md 定位 → 只读目标函数的片段 → replace_in_file → 验证。严格按 Step 1-6 执行。

### T2 — 跨模块修改

| 特征 | 示例 |
|------|------|
| 涉及 4-7 个文件的状态流转 | "改角色升级逻辑，涉及 stats.py / level.py / ui.py" |
| 重构一个子系统 | "把所有 NPC 对话从 if-else 改成状态机" |
| 修一个跨模块的复杂 bug | "数据在 A 文件改了但 B 文件没同步" |
| 新增一类功能 | "加魅力值系统，涉及 5 个文件" |
| CODE_INDEX.md 的「功能注入模式」表正好匹配 | |

**策略：函数级阅读模式** — 从 CODE_INDEX.md 的注入模式表出发 → 读取涉及的完整函数/类（不限 30 行）→ 允许读 ≤ 5 个完整文件 → 仍然禁止全项目 grep/glob。

**T1 升级到 T2 的判断：** 如果 Step 2 查表时发现需要用 4+ 个文件，或者 CODE_INDEX.md 的注入模式表给出了 4+ 步清单 → 自动升级为 T2。

### T3 — 架构变更

| 特征 | 示例 |
|------|------|
| 替换框架/库 | "把 Flask 换成 FastAPI" |
| 替换交互模式 | "把 SSE 换成 WebSocket" |
| 重写整个前端 | "把 Jinja2 模板换成 React SPA" |
| 换数据库/存储方案 | "SQLite 换 PostgreSQL" |
| 涉及文件 > 7 个 | |

**策略：全读模式** — 允许全项目 grep、允许递归扫描目录、允许读取任意文件的全文。

**必须执行：**
- 先向用户确认变更方案（列出入侵范围）
- 从 CODE_INDEX.md 的「架构依赖关系」表了解影响范围
- 修改完成后，更新 CODE_INDEX.md 的架构依赖关系表和文件地图

### 级别自动切换规则

```
读完 CODE_INDEX.md
    │
    ├── 只有"按功能快速定位"表能回答 → T1（严格片段模式）
    │
    ├── "功能注入模式"表给出了 4+ 步清单 → T2（函数级阅读模式）
    │
    ├── 任务涉及"换框架/换交互/换数据库"等架构关键词 → 直接 T3
    │
    └── 不确定 → 默认 T2，保守策略：多读一点比漏读安全
```

**NEVER：** 在 T2 任务上硬套 T1 规则（比如只读 30 行来修一个跨 5 文件的 bug）。
**NEVER：** 在 T1 任务上使用 T3 策略（比如改个常量值就全项目扫描）。

---

## 1. 工作流（按级别执行）

### T1 级工作流（默认，精准修改）

```
[ ] Step 1: read_file CODE_INDEX.md
    ↓
[ ] Step 2: 在"按功能快速定位"表中找到目标文件
    ↓
[ ] Step 3: read_file 目标文件（offset/limit 只读目标区域，不读全文）
    ↓
[ ] Step 4: replace_in_file 精准修改（每次 10~50 行）
    ↓
[ ] Step 5: 验证修改（lint / import check）
    ↓
[ ] Step 6: 检查 CODE_INDEX.md 是否需要轻量更新
```

### T1 各步骤细则

#### Step 1 — 读取 CODE_INDEX.md
```
WHEN 收到任何任务:
    IMMEDIATELY: read_file CODE_INDEX.md
    DO NOT: 先读源代码、先扫描目录
    THEN: 执行任务分级（Section 0）
```

#### Step 2 — 查表定位（T1 模式）
```
READ "按功能快速定位"表
MATCH 用户需求 → ≤ 3 个目标文件路径
IF 匹配不到 OR 需要 4+ 个文件 → 升级到 T2
```

#### Step 3 — 按需读取（T1 模式）
```
READ 目标文件，指定 offset/limit 只读取目标函数/区块
HARD LIMIT: 最多读 3 个文件，每个只读 ≤ 60 行
```

#### Step 4-6 — 修改、验证、维护索引
```
同通用规则：replace_in_file 精准修改 → 验证 → 轻量更新索引
详见下方 T2/T3 共用部分。
```

---

### T2 级工作流（跨模块修改）

```
[ ] Step 1: read_file CODE_INDEX.md，重点关注"功能注入模式"表
    ↓
[ ] Step 2: 按注入模式表的步骤清单，逐一处理每个文件
    ↓          每文件只读涉及的函数/类（可读完整函数，不限 30 行）
    ↓          总文件数 ≤ 5
    ↓
[ ] Step 3: 按注入模式表的顺序逐一修改
    ↓
[ ] Step 4: 跨文件验证（检查所有受影响文件的交互是否正确）
    ↓
[ ] Step 5: 更新 CODE_INDEX.md（可能涉及多行更新，≤ 5 行）
```

#### T2 读取策略

```
READ 方式: 允许读取完整函数/类，不限行数
HARD LIMIT: 总文件数 ≤ 5
NEVER: grep -r 全项目搜索（仍从 CODE_INDEX.md 定位）
NEVER: 递归遍历目录
```

#### T2 验证策略

```
AFTER 全部修改完成:
    逐个检查所有改动的文件: 导入正确？逻辑完整？
    如有跨文件调用: 确认函数签名一致
    运行所有改动文件的 lint
```

---

### T3 级工作流（架构变更）

```
[ ] Step 1: read_file CODE_INDEX.md，重点关注"架构依赖关系"表
    ↓          了解哪些文件受影响、哪些文件安全
[ ] Step 2: 向用户确认变更方案
    ↓          列出入侵范围（文件名清单 + 变更类型）
    ↓          用户确认后才能继续
[ ] Step 3: 全读模式 — 允许 grep -r、允许递归 list、允许读全文
    ↓
[ ] Step 4: 执行变更（可能涉及大量重写）
    ↓
[ ] Step 5: 全面验证（lint + import check + 功能测试）
    ↓
[ ] Step 6: 完整更新 CODE_INDEX.md — 重写文件地图、注入模式、架构依赖
```

#### T3 约束

```
ALLOWED: grep -r、glob 搜索、list_files 递归、读取完整文件、write_to_file 重写
MUST: 先确认方案再动手（列出所有受影响文件清单）
MUST: 完成后更新 CODE_INDEX.md 的所有相关表
```

---

## 2. T1/T2/T3 共用规则

### 修改规则（所有级别共用）

```
USE replace_in_file 精准修改
EACH 替换: 10~50 行（T1），10~100 行（T2），不限制（T3）
NEVER: write_to_file 覆盖已有文件（T3 架构重写除外）
NEVER: 对同一文件连续 replace 超过 3 次不重新 read
```

### 验证规则（所有级别共用）

```
IMMEDIATELY AFTER 修改:
    Python: python -c "from module import func"
    任何项目: read_lints 目标文件
IF 报错: 立即修复
```

### 索引维护规则（所有级别共用，Step 6）

**索引不维护 = 一次性工具 = 协议失效。**

```
T1: 最多改 3 行（文件地图追加/删除/更新一行）
T2: 最多改 5 行（可能新增注入模式条目）
T3: 重写相关表（文件地图 + 注入模式 + 架构依赖）
```

---

## 3. 反例对照表

以下是 AI 的**典型错误行为**，以及对应正确行为：

| ❌ 错误行为 | ✅ 正确行为 | 为什么错 |
|------------|-----------|---------|
| 修跨 5 文件 bug 时仍然只读 30 行片段 | 识别为 T2，升级到函数级阅读 | T1 策略不够，硬套会漏隐式依赖 |
| 改一个常量值就全项目 grep 搜索 | 识别为 T1，从索引定位后只读 constants.py 的 3 行 | T3 策略过度，浪费 95% token |
| 收到"改登录"，直接 read_file auth.py | 先 read_file CODE_INDEX.md，查到"登录 → auth.py"，再读 | 项目可能有 auth.py / login.py / auth_service.py |
| 读 800 行 app.py 全文来改一个函数 | offset/limit 只读目标函数的 60 行 | 其他 740 行与任务无关 |
| 新建文件后不更新 CODE_INDEX.md | 追加文件地图 + 注入模式（如适用） | 下次任务索引找不到新文件，AI 退化 |
| 换框架时不看架构依赖关系表 | 先看哪些文件受影响、哪些安全 | 浪费时间读无关模块 |

---

## 4. 工具使用规则（按级别）

### read_file

| 级别 | 允许 | 禁止 |
|------|------|------|
| T1 | 读 CODE_INDEX.md + ≤ 3 个目标文件片段（≤ 60 行/文件） | 读完整文件、连续读 3+ 个文件 |
| T2 | 读 CODE_INDEX.md + ≤ 5 个文件的完整相关函数 | grep/glob 搜索、递归遍历 |
| T3 | 无限制（先确认方案） | 确认前动手修改 |

### search_content (grep)

| 级别 | 允许 | 禁止 |
|------|------|------|
| T1 | 在已定位的 ≤ 3 个文件中搜索 | 全项目 grep |
| T2 | 在 ≤ 5 个目标文件中搜索 | 全项目 grep（范围过大） |
| T3 | 全项目 grep 允许 | 无 |

### replace_in_file / write_to_file

所有级别共用，见 Section 2。

---

## 5. 代码修改规则

- 保持现有代码风格（缩进、命名、引号风格）
- 不添加与任务无关的 import、注释、emoji
- 不在业务代码中硬编码数值（先查 constants.py 或等价配置文件）
- 不引入新外部依赖，除非用户明确要求

---

## 6. 回复规则

- 修改完成后用简洁列表说明：什么级别(T1/T2/T3)、改了什么文件、什么函数、为什么
- T2/T3 修改前先告诉用户"这是 T2/T3 级变更，涉及 X 个文件"
- 有错误先自行修复，修复不了再告知用户
- 不提"我先读一下 CODE_INDEX.md"——直接做，做完汇报结果

---

## 移植指南

```
# 按目标平台命名复制
cp AGENTS.md /target/project/AGENTS.md       # Codex / Qwen / Augment
cp AGENTS.md /target/project/CLAUDE.md       # Claude Code
cp AGENTS.md /target/project/CODEBUDDY.md    # CodeBuddy
cp AGENTS.md /target/project/GEMINI.md       # Gemini CLI

# IDE 适配器（可选）
# .cursor/rules/project.mdc           → Cursor
# .windsurfrules                      → Windsurf
# .github/copilot-instructions.md     → GitHub Copilot
# .codebuddy/rules/project-readme.md  → CodeBuddy IDE
```
