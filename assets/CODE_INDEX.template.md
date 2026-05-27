# {{PROJECT_NAME}} — 项目代码索引

> 本文件是项目专属数据。行为规范见 [AGENTS.md](./AGENTS.md)。
>
> 这是 **Agent 的第一站**。每次修改代码前，Agent 必须先读这个文件，
> 从"按功能快速定位"表中找到目标文件，再精准读取和修改。

---

## 文件地图：谁管什么

> 列出项目的核心文件。Agent 从这里了解整个项目骨架。

| 文件 | 一句话职责 | 关键符号 |
|------|-----------|----------|
| `app.py` | 应用入口，注册蓝图和中间件 | `create_app()`, `register_blueprints()` |
| `routes/api.py` | REST API 路由定义 | `api_bp`, `get_users()`, `create_order()` |
| `models/user.py` | 用户数据模型 | `class User`, `User.query` |
| `templates/index.html` | 首页模板 | `<div id="main">` |
| `config.py` | 全局配置（数据库、密钥） | `Config`, `DATABASE_URL` |

<!--
  填写规范：
  - 文件路径：相对于项目根目录
  - 一句话职责：这个文件是干什么的，10~15 个字以内
  - 关键符号：Agent 搜索时用到的函数名、类名、变量名、HTML id
  - 上面是示例，实际填写时替换为真实项目内容
-->

---

## 按功能快速定位

> ⭐ **这是索引的核心。** Agent 查这张表就知道改哪个文件。

| 用户想做什么 | 改哪个文件 | 关键函数/组件 |
|------------|-----------|-------------|
| 修改首页布局 | `templates/index.html` | `<div id="main">` 区域 |
| 添加一个新 API 接口 | `routes/api.py` | 新增 `def xxx():` |
| 修改用户数据模型 | `models/user.py` | `class User` |
| 调整全局配置 | `config.py` | `class Config` |

<!--
  填写规范：
  - 第一列：用日常语言描述用户需求，不是技术术语
    好的："添加新页面" / "修改订单金额计算逻辑"
    坏的："修改 order_service.py 的 calculate 函数"（这是结论不是问题）
  - 第二列：相对于项目根目录的文件路径
  - 第三列：在该文件中要定位的具体符号
  - 最少 3 行，推荐 5~10 行
-->

---

## 路由/API 速查表

> 如果项目有 HTTP API 或页面路由，填这个表。纯 CLI 工具可删除本节。

| 路由 | 方法 | 说明 |
|------|------|------|
| `/` | GET | 首页 |
| `/api/users` | GET | 获取用户列表 |
| `/api/users/<id>` | GET | 获取单个用户 |
| `/api/orders` | POST | 创建订单 |

---

## 数据流概览

> 一句话说清楚数据怎么走的。没有复杂数据流可以写"无"。

```
用户请求 → Flask 路由 → SQLAlchemy ORM → SQLite 数据库
                                          ↓
用户 ← Jinja2 模板 ← 查询结果
```

---

## 关键注意事项

> 容易踩坑的点，Agent 开始改代码前必须知道。

1. **配置文件**：数据库连接串在 `config.py` 的 `DATABASE_URL` 变量，不要在其他文件硬编码
2. **编码约定**：Python 文件统一 `# -*- coding: utf-8 -*-`，缩进 4 空格
3. **模板路径**：Jinja2 模板在 `templates/` 目录，引用时不加前缀
4. **静态资源**：CSS/JS/图片放在 `static/` 目录，模板中用 `{{ url_for('static', filename='...') }}`

<!--
  常见注意事项类型：
  - 配置文件位置和关键变量名
  - 编码/缩进/命名风格约定
  - 特殊文件结构（如 templates 路径、static 路径）
  - 硬编码数据的位置
  - 第三方依赖的特殊用法
-->
