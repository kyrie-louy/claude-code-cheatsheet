# Claude Code 速查表

> 按"我想做什么"组织，3 秒扫到分类，10 秒找到说法，30 秒开始用。

---

## A. 快速上手

```bash
# 安装
npm install -g @anthropic-ai/claude-code

# 启动（在项目目录下）
cd 项目目录 && claude
```

### 会话管理

| 命令 | 说明 |
|------|------|
| `claude` | 启动新对话 |
| `claude -c` / `claude --continue` | 继续最近一次对话 |
| `claude --resume` | 从历史对话列表中选择一个继续 |
| `claude --resume <session-id>` | 继续指定的历史对话 |
| `claude -p "问题"` | 非交互模式（直接输出结果，适合脚本） |
| `claude --model opus` | 指定模型启动（`opus` / `sonnet` / `haiku`） |
| `claude --verbose` | 显示详细日志 |

---

## B. 命令速查（已启动 Claude 后使用）

### Slash 命令

| 命令 | 作用 | 什么时候用 |
|------|------|-----------|
| `/help` | 查看帮助 | 忘了命令时 |
| `/compact` | 压缩上下文 | 对话太长、快满时 |
| `/compact [指令]` | 带指令压缩 | 只保留某部分上下文 |
| `/clear` | 清空对话 | 换任务时 |
| `/model` | 切换模型 | 需要换更强/更快的模型 |
| `/cost` | 查看本次花费 | 想看看花了多少钱 |
| `/review` | 代码审查 | 基于 git diff 审查改动 |
| `/rewind` | 撤销上一步 | Claude 改错了想回退 |
| `/memory` | 查看/编辑记忆 | 管理持久化记忆 |
| `/init` | 初始化 CLAUDE.md | 快速创建项目规则文件 |
| `/doctor` | 诊断配置 | 遇到问题先跑这个 |
| `/context` | 查看上下文使用 | 看还剩多少上下文空间 |
| `/resume` | 选择历史对话继续 | 在会话中切换到其他对话 |

### 快捷键

| 快捷键 | 作用 |
|--------|------|
| `Shift+Tab` | 切换模式：普通 → 自动接受 → Plan 模式 |
| `Tab` | 开关深度思考（Extended Thinking） |
| `Esc Esc` | 打开撤销菜单 |
| `#` | 添加记忆 |
| `Ctrl+C` | 中断当前操作 |

---

## C. 高频场景（推荐说法 + 操作）

### 0. 指定文件/代码

CLI 里不需要像 Cursor 那样拖拽或 @ 引用，直接说路径或贴代码就行：

| 我想... | 做法 |
|---------|------|
| 指定某个文件 | 直接说路径：`"帮我看看 src/utils/auth.ts"` |
| 指定某个文件夹 | 直接说路径：`"分析 src/components/ 下所有组件"` |
| 改一小段代码 | 直接把代码粘贴到对话框，说要改什么 |

> **说明：** Claude 会自动读取你提到的文件，像跟人说话一样就行。VS Code 里则可以用 `@文件名` 或选中代码后提问（见 E 区）。

### 1. 看懂项目/代码

```
"帮我梳理这个项目的整体架构"
"解释这个函数的逻辑"
"这个模块和哪些文件有依赖关系？"
```

> **操作：** 直接在 Claude 中输入即可。Claude 会自动读取项目文件。

### 2. 修改代码

```
"把这个函数改成 async 的，只改最小范围"
"把所有 var 替换成 const/let"
```

> **技巧：** 加上"只改最小范围"防止过度重构。怕改错先 `Shift+Tab` 切到 Plan 模式。

### 3. 修 Bug

```
"这个报错的 root cause 是什么？先分析再给 patch"
"运行测试，失败的话帮我修"
```

> **技巧：** 让它先分析原因，别让它直接改。修之前先 `git commit` 保底。

### 4. 加新功能

```
"先给我实现方案，别直接写代码"          ← 先审方案
"按你说的方案开始实现吧"              ← 确认后再动手
```

> **操作：** `Shift+Tab` 切到 Plan 模式 → 看方案 → 确认后再切回普通模式执行。

### 5. 跑命令/测试

```
"运行 npm run test，失败的话帮我修"
"执行 npm run build 看看有没有报错"
"帮我跑 pnpm lint 并自动修复"
```

> **说明：** Claude 可直接执行终端命令，不用切出去。

### 6. 记住项目规则

| 方法 | 适用场景 |
|------|---------|
| 输入 `#` + 规则内容 | 当前会话立即记住 |
| `/memory` | 查看和编辑已有记忆 |
| `/init` → 编辑 `CLAUDE.md` | 长期项目规则（团队共享） |
| 编辑 `~/.claude/CLAUDE.md` | 跨项目的个人偏好 |

---

## D. 项目记忆与规则

| 层级 | 文件/方式 | 作用域 |
|------|----------|--------|
| 项目级 | `CLAUDE.md`（项目根目录） | 当前项目，团队共享 |
| 用户级 | `~/.claude/CLAUDE.md` | 所有项目，个人偏好 |
| 会话级 | `#` 或 `/memory` | 当前会话 |
| 一次性 | 直接写在 prompt 里 | 仅本次对话 |

> 用 `/init` 一键创建 `CLAUDE.md`，把常用规范写进去就不用每次重复了。

---

## E. Skills（自定义技能）

Skills 是可复用的扩展能力包，装一次、反复用。放到指定目录即可自动加载。

### 常用操作

| 操作 | 做法 |
|------|------|
| 查看已有 skill | `/skills` |
| 使用 skill | 输入 `/skill名称`，如 `/simplify`、`/batch` |
| 安装 skill（插件系统） | `/plugin` → 选择安装 |
| 安装 skill（对话里说） | 直接说"安装 xxx skill"，Claude 会帮你操作 |
| 安装 skill（手动） | 把 skill 文件夹复制到 `~/.claude/skills/` |
| 安装 skill（npx） | `npx add-skill <仓库名>` |
| 卸载 skill | 删除 `~/.claude/skills/对应文件夹/` |
| 自己写 skill | 创建 `~/.claude/skills/名称/SKILL.md` |

### Skill 存放位置

| 位置 | 作用域 |
|------|--------|
| `~/.claude/skills/` | 个人全局，所有项目可用 |
| `.claude/skills/`（项目目录下） | 项目级，可提交到 git 团队共享 |

### 内置 Skill

| 名称 | 作用 |
|------|------|
| `/simplify` | 审查最近改动的代码质量，自动修复 |
| `/batch <指令>` | 跨文件批量执行大规模改动 |

> **说明：** `SKILL.md` 中的 `name` 字段就是 `/slash-command` 名称。skill 太多会占上下文，用 `/context` 检查。

---

## F. MCP（外部工具集成）

MCP（Model Context Protocol）让 Claude 连接外部工具和服务。

### 常用命令

| 命令 | 说明 |
|------|------|
| `claude mcp add <名称>` | 添加 MCP 服务器 |
| `claude mcp add-json <名称> '<json>'` | 用 JSON 配置直接添加 |
| `claude mcp list` | 查看已安装的 MCP 服务器 |
| `claude mcp get <名称>` | 查看/测试某个 MCP 服务器 |
| `claude mcp remove <名称>` | 移除 MCP 服务器 |

### 配置层级

| 层级 | 文件 | 说明 |
|------|------|------|
| 项目级 | `.mcp.json`（项目根目录） | 团队共享，可提交到 git |
| 用户级 | `~/.claude.json` | 个人全局，所有项目可用 |

### 示例：添加 GitHub MCP

```bash
claude mcp add github --scope user -- npx -y @modelcontextprotocol/server-github
```

> **说明：** 添加或修改 MCP 后需要重启 Claude Code 才能生效。敏感信息（API Key 等）可用环境变量 `${VAR}` 语法引用。

---

## G. VS Code 用户专区

| 我想... | 做法 |
|---------|------|
| 打开 Claude | 点击右上角 ✱ 图标，或状态栏 Claude Code |
| 引用某个文件 | 输入 `@文件名` 或用 `Option+K` |
| 引用选中的代码 | 先选中代码，再在 Claude 面板提问 |
| 怕它乱改 | 用 Plan 模式（`Shift+Tab` 切换），先看方案再执行 |
| 看实时修改 | 开启 auto-accept，实时看 diff |
| 开新对话 | `Cmd+N` |

---

## H. 避坑指南

1. **不要一句话塞太多需求** — 拆成小步骤，一次一个任务
2. **大改之前先出 plan** — `Shift+Tab` 切到 Plan 模式，先看方案再执行
3. **项目规范写进 CLAUDE.md** — 不用每次重复说
4. **上下文快满了用 `/compact`** — 换任务用 `/clear`
5. **重要改动前先 git commit** — 方便回退
6. **遇到问题先 `/doctor` 诊断** — 排查配置和环境问题
