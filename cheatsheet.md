# Claude Code 速查表

> 按"我想做什么"组织，3 秒扫到分类，10 秒找到说法，30 秒开始用。

---

## A. 启动与配置

### 安装

```bash
# 推荐方式
curl -fsSL https://claude.ai/install.sh | bash

# 备选方式
npm install -g @anthropic-ai/claude-code
brew install --cask claude-code
```

### 启动

```bash
cd 项目目录 && claude
```

### 升级

```bash
claude update

# Homebrew 安装：
brew upgrade claude-code
```

> 原生安装（curl）支持自动更新，也可手动 `claude update` 触发

### CLI 启动参数

| 命令 | 说明 |
|------|------|
| `claude` | 启动新对话 |
| `claude -c` | 继续最近一次对话 |
| `claude --resume` | 从历史对话列表中选择一个继续 |
| `claude -r <id>` | 继续指定的历史对话（`--resume` 短参数） |
| `claude -p "问题"` | 非交互模式（直接输出结果，适合脚本） |
| `claude --model opus` | 指定模型启动（`opus` / `sonnet` / `haiku`） |
| `claude --verbose` | 显示详细日志 |
| `claude -w` | 工作树隔离模式（`--worktree`） |

### 管道输入

| 用法 | 说明 |
|------|------|
| `cat file.ts \| claude "解释这段代码"` | 把文件内容传给 Claude |
| `git diff \| claude "review 这个改动"` | 把 diff 传给 Claude 审查 |
| `cat error.log \| claude -p "分析错误原因"` | 分析日志，非交互输出结果 |

### claude config 系列

| 命令 | 说明 |
|------|------|
| `claude config set` | 设置配置项 |
| `claude config get` | 查看某个配置项 |
| `claude config list` | 列出所有配置 |

> 配置文件位置：`~/.claude.json`（全局）、项目根目录 `.claude/config.json`（项目级）

---

## B. 会话内操作

### Slash 命令

| 命令 | 作用 | 什么时候用 |
|------|------|-----------|
| `/help` | 查看帮助 | 忘了命令时 |
| `/compact` | 压缩上下文 | 对话太长、快满时 |
| `/compact [指令]` | 带指令压缩 | 只保留某部分上下文 |
| `/clear` | 清空对话 | 换任务时 |
| `/model` | 切换模型 | 换更强/更快的模型 |
| `/cost` | 查看本次花费 | 想看花了多少钱 |
| `/diff` | 查看未提交的 diff | 看当前改了什么 |
| `/rewind` | 撤销上一步 | Claude 改错了想回退 |
| `/memory` | 查看/编辑记忆 | 管理持久化记忆 |
| `/init` | 初始化 CLAUDE.md | 快速创建项目规则文件 |
| `/doctor` | 诊断配置 | 遇到问题先跑这个 |
| `/context` | 查看上下文使用 | 看还剩多少空间 |
| `/resume` | 选择历史对话 | 在会话中切换到其他对话 |
| `/mcp` | 管理 MCP 连接和 OAuth | 添加/管理 MCP 服务器 |
| `/config` | 打开设置界面 | 修改配置（别名 `/settings`） |
| `/permissions` | 查看/更新权限 | 管理工具权限 |
| `/fast` | 切换快速模式 | 加速输出（同模型） |
| ~~`/review`~~ | ~~代码审查（已弃用）~~ | 改用 code-review 插件 |

### 快捷键

| 快捷键 | 作用 |
|--------|------|
| `Shift+Tab` / `Alt+M` | 切换模式：普通 → 自动接受 → Plan 模式 |
| `Option+T`（macOS）/ `Alt+T` | 开关深度思考（Extended Thinking） |
| `Esc Esc` | 打开撤销菜单 |
| `#` | 添加记忆 |
| `@` | 文件路径提及（触发自动补全） |
| `?` | 查看可用快捷键 |
| `Ctrl+C` | 中断当前操作 |
| `Ctrl+D` | 退出会话 |
| `Ctrl+L` | 清屏 |

### 直接执行命令（Bash 模式）

| 用法 | 说明 |
|------|------|
| `! <command>` | 在会话内直接执行 bash 命令，如 `! ls`、`! git status` |
| `! npm run test` | 直接跑测试 |
| `! cat src/index.ts` | 查看文件内容 |

> 输入 `!` 前缀 + 空格 + 命令，不用退出 Claude 就能执行终端命令（`!` 紧跟命令也可以）

---

## C. Skills 与 MCP

### Skills（自定义技能）

| 操作 | 做法 |
|------|------|
| 查看已有 skill | `/skills` |
| 使用 skill | 输入 `/skill名称`，如 `/simplify`、`/batch` |
| 安装（插件系统） | `/plugin` → 选择安装 |
| 安装（对话里说） | 直接说"安装 xxx skill"，Claude 帮你操作 |
| 安装（手动） | 把 skill 文件夹复制到 `~/.claude/skills/` |
| 安装（npx） | `npx add-skill <仓库名>` |
| 卸载 skill | 删除 `~/.claude/skills/对应文件夹/` |
| 自己写 skill | 创建 `~/.claude/skills/名称/SKILL.md` |

### Skill 存放位置

| 位置 | 作用域 |
|------|--------|
| `~/.claude/skills/` | 个人全局，所有项目可用 |
| `.claude/skills/`（项目下） | 项目级，可提交 git 共享 |

### 内置 Skill

| 名称 | 作用 |
|------|------|
| `/simplify` | 审查最近改动的代码质量，自动修复 |
| `/batch <指令>` | 跨文件批量执行大规模改动 |
| `/debug` | 诊断当前会话 |
| `/loop` | 定时重复执行 prompt |
| `/claude-api` | 加载 Claude API 参考 |

> skill 太多会占上下文，用 `/context` 检查是否有被排除的 skill

### MCP 常用命令

| 命令 | 说明 |
|------|------|
| `claude mcp add <名称>` | 添加 MCP 服务器 |
| `claude mcp add-json <名称> '<json>'` | 用 JSON 配置直接添加 |
| `claude mcp list` | 查看已安装的 MCP 服务器 |
| `claude mcp get <名称>` | 查看/测试某个服务器 |
| `claude mcp remove <名称>` | 移除 MCP 服务器 |

### MCP 配置层级

| 层级 | 说明 |
|------|------|
| `local`（默认） | 个人，当前项目 |
| `project` | 团队共享，存 `.mcp.json` |
| `user` | 个人全局 |

### 示例：添加 GitHub MCP

```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
```

> 添加或修改 MCP 后需重启 Claude Code 生效。API Key 等敏感信息可用 `${VAR}` 环境变量引用

---

## D. 机制详解

### 项目记忆与规则（CLAUDE.md 层级）

| 层级 | 文件/方式 | 作用域 |
|------|----------|--------|
| 项目级 | `CLAUDE.md`（项目根目录） | 当前项目，团队共享 |
| 用户级 | `~/.claude/CLAUDE.md` | 所有项目，个人偏好 |
| 会话级 | 用 `#` 或 `/memory` | 当前会话 |
| 一次性 | 直接写在 prompt 里 | 仅本次对话 |

> 用 `/init` 一键创建 `CLAUDE.md`，把常用规范写进去就不用每次重复了

### 上下文管理机制

| 情况 | 操作 | 效果 |
|------|------|------|
| 对话太长/快满 | `/compact` | 压缩上下文，保留关键信息 |
| 只保留部分 | `/compact [指令]` | 按指令选择性压缩 |
| 换任务 | `/clear` | 清空对话，重新开始 |
| 想知道剩余空间 | `/context` | 查看上下文使用情况 |
| 自动压缩 | （自动触发） | 快满时 Claude 自动压缩历史消息 |

### 权限模式说明

| 模式 | 说明 | 切换 |
|------|------|------|
| 普通模式 | 每次文件修改/命令执行需要你确认 | 默认 |
| 自动接受 | 自动接受 Claude 的文件修改 | `Shift+Tab` / `Alt+M` |
| Plan 模式 | 只出方案不执行，适合大改前审查 | `Shift+Tab` / `Alt+M` 再按一次 |

> 大改之前先切 Plan 模式看方案，确认后再切回普通模式执行

---

## E. 进阶技巧

### 指定文件/代码

```
"帮我看看 src/utils/auth.ts"
"分析 src/components/ 下所有组件"
直接粘贴代码 → "帮我把这段改成 TypeScript"
```

> CLI 里不需要 @ 或拖拽，直接说路径或贴代码就行。VS Code 里可用 `@文件名`

### 看懂项目/代码

```
"帮我梳理这个项目的整体架构"
"解释这个函数的逻辑"
"这个模块和哪些文件有依赖关系？"
```

> 直接输入即可，Claude 会自动读取项目文件

### 修改代码

```
"把这个函数改成 async 的，只改最小范围"
"把所有 var 替换成 const/let"
```

> 加"只改最小范围"防止过度重构。怕改错先 `Shift+Tab` 切 Plan 模式

### 修 Bug

```
"这个报错的 root cause 是什么？先分析再给 patch"
"运行测试，失败的话帮我修"
```

> 让它先分析原因，修之前先 `git commit` 保底

### 加新功能

```
"先给我实现方案，别直接写代码"  ← 先审方案
"按你说的方案开始实现吧"      ← 确认后动手
```

> `Shift+Tab` 切 Plan 模式 → 看方案 → 确认后切回普通模式执行

### 跑命令/测试

```
"运行 npm run test，失败的话帮我修"
"执行 npm run build 看看有没有报错"
"帮我跑 pnpm lint 并自动修复"
```

> Claude 可直接执行终端命令，不用切出去。也可用 `!` 前缀直接跑

### 避坑指南

1. **不要一句话塞太多需求** — 拆成小步骤，一次一个任务
2. **大改之前先出 plan** — `Shift+Tab` 切到 Plan 模式，先看方案再执行
3. **项目规范写进 CLAUDE.md** — 不用每次重复说
4. **上下文管理** — 快满了用 `/compact`，换任务用 `/clear`
5. **重要改动前先 git commit** — 方便回退
6. **遇到问题先 /doctor 诊断** — 排查配置和环境问题

### VS Code 专区

| 我想... | 做法 |
|---------|------|
| 打开 Claude | 点击右上角 ✱ 图标，或状态栏 Claude Code |
| 引用某个文件 | 输入 `@文件名` 或用 `Option+K` |
| 引用选中的代码 | 先选中代码，再在 Claude 面板提问 |
| 怕它乱改 | 用 Plan 模式（`Shift+Tab` 切换），先看方案再执行 |
| 看实时修改 | 开启 auto-accept，实时看 diff |
| 开新对话 | `Cmd+N` |
