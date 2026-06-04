# Dida-Task

滴答清单（Dida365）任务管理工具。通过官方 OpenAPI + 私有 API，让 AI agent 能读取（任务、清单、已完成任务、番茄/专注统计）和写入（创建、更新、完成、删除任务和清单）滴答清单的数据。

一个 Claude Code skill，不是独立 App，也不是 MCP server。

## 何时使用

**适合：**
- 想让 AI 助手能直接操作个人 TODO 的滴答清单（国内版）用户
- 用户说"加个任务"、"提醒我..."、"今天完成了什么"、"专注了多久"、"花了几个番茄在 X 上"等场景

**不支持：**
- ❌ **TickTick 国际版** —— 只支持国内 dida365.com（数据账号不互通）
- ❌ **自动 cookie 续期** —— Cookie 失效后需手动从浏览器复制
- ❌ **MCP 协议** —— 当前只是 skill，未来可能扩展

## 安装

```bash
# 1. 克隆/复制到你的 skills 目录
cp -r dida-task ~/.claude/skills/

# 2. 首次配置（OAuth + cookie）
python3 ~/.claude/skills/dida-task/scripts/dida.py setup

# 3. 验证
python3 ~/.claude/skills/dida-task/scripts/dida.py health
```

详细配置见 [setup.md](setup.md)。

## 能力速查

所有命令统一入口：`python3 scripts/dida.py <子命令>`，全部支持 `--json` 输出。

### 读

| 命令 | 作用 |
|---|---|
| `read list-projects` | 列所有清单 |
| `read list-tasks <project>` | 列某清单下未完成任务 |
| `read get-task <task-id>` | 单个任务详情 |
| `read search <关键词>` | 全量任务模糊搜索 |
| `read completed-today` | 今天已完成任务 |
| `read completed-range <from> <to>` | 按日期范围拉已完成任务 |

### 番茄/专注

| 命令 | 作用 |
|---|---|
| `pomo today` | 今天专注总时长 + 段数 |
| `pomo by-task <task-id-或关键词>` | 某任务花了几个番茄 / 多少分钟 |
| `pomo stats <from> <to>` | 按日期范围的专注统计（heatmap + 项目分布） |

### 写

| 命令 | 作用 |
|---|---|
| `write create-task --title <标题> [选项]` | 创建任务 |
| `write update-task <task-id> [选项]` | 更新任务 |
| `write complete-task <task-id>` | 标记完成 |
| `write delete-task <task-id>` | 删除任务（默认 dry-run，加 `--confirm` 才真删） |
| `write create-project --name <名字> [选项]` | 创建清单 |
| `write update-project <project-id> [选项]` | 更新清单 |
| `write delete-project <project-id>` | 删除清单（默认 dry-run） |

### 元

| 命令 | 作用 |
|---|---|
| `setup` | 首次配置（OAuth + cookie） |
| `refresh-token` | OAuth token 过期时重走授权 |
| `refresh-cookie` | Cookie 失效时引导更新 |
| `health` | 检查 token / cookie 状态 |

## 重要声明：关于私有 API

本 skill **读取已完成任务、番茄统计、全量搜索**走的是滴答的**非公开私有 API**（用浏览器 cookie 鉴权）：

- ✅ 功能完整（这些数据官方 OpenAPI 没有）
- ⚠️ 非官方，滴答有权随时改动或封禁
- ⚠️ Cookie 会过期（一般几周到几个月），需手动续期
- ⚠️ 仅供**个人使用**，请勿用于商业产品或大规模爬取

所有**写操作**（建/改/删任务和清单）走官方 OpenAPI，稳定可靠。

如果你只需要写任务和读未完成任务，可在 setup 时跳过 cookie，只用官方 OpenAPI。

## 凭证安全

- 配置文件 `~/.config/dida-task/credentials.json` 权限为 600
- 也支持环境变量（优先级更高）：`DIDA_CLIENT_ID`、`DIDA_CLIENT_SECRET`、`DIDA_ACCESS_TOKEN`、`DIDA_COOKIE_T`
- 永远不要把 Client Secret / Access Token / Cookie 提交到 git 或公开分享

## 文档

- [SKILL.md](SKILL.md) — Agent 使用入口
- [setup.md](setup.md) — 首次配置详细引导（如何申请 client_id、拿 cookie）
- [examples/usage.md](examples/usage.md) — 常见用法示例
- [api-reference.md](api-reference.md) — 官方 + 私有 API 端点速查

## License

MIT
