# MewCode

终端 AI 编程助手，基于 Textual 打造 TUI 界面，支持多 Agent 团队协作和深度工具集成。

## 功能特性

- **终端 TUI** — 基于 Textual 的丰富界面，支持聊天历史、工具调用块展示、权限弹窗
- **多 Provider 支持** — 兼容 Anthropic Claude、OpenAI 及 OpenAI 兼容 API
- **多 Agent 团队** — 派生子 Agent、协调团队，支持共享任务板和 Agent 间消息通信
- **MCP 集成** — 接入 Model Context Protocol 服务，扩展工具能力
- **技能系统** — 可插拔的技能机制，内置技能 + 项目本地技能
- **Worktree 隔离** — 基于 git worktree 的隔离环境，Agent 并行执行互不干扰
- **记忆系统** — 基于文件的持久化记忆，支持相关性召回
- **Hook 事件** — 事件驱动的钩子系统（启动、退出、工具执行等）
- **权限模式** — `default` / `accept-edits` / `plan` / `bypass`，`Shift+Tab` 一键切换
- **Plan 模式** — 先出方案，用户审核通过后再执行
- **沙箱** — 可选的 OS 级沙箱（Linux 用 bubblewrap，macOS 用 Seatbelt）
- **会话管理** — 支持恢复历史会话并附带摘要
- **非交互模式** — `-p` 参数直接从命令行执行 prompt（支持 JSON 流式输出）
- **Remote 模式** — 启动 WebSocket 服务，通过浏览器访问 `localhost:18888`

## 安装

### 环境要求

- Python 3.11+
- [uv](https://docs.astral.sh/uv/)（推荐）或 pip

### 安装步骤

```bash
# 克隆仓库
git clone https://github.com/xxstar-01/MewCode.git
cd MewCode

# 使用 uv 安装
uv sync

# 或使用 pip
pip install -e .
```

### 配置

在项目根目录或 `.mewcode/config.yaml` 中创建配置文件：

```yaml
providers:
  - name: claude
    protocol: anthropic
    base_url: https://api.anthropic.com/v1
    model: claude-sonnet-5-20250901
    # api_key: sk-ant-...  # 也可以通过环境变量 ANTHROPIC_API_KEY 设置

permission_mode: default  # default | accept-edits | plan | bypass
```

API Key 从环境变量自动读取：
- Anthropic：`ANTHROPIC_API_KEY`
- OpenAI / OpenAI 兼容：`OPENAI_API_KEY`

## 使用方式

### 交互模式

```bash
uv run mewcode
```

### 非交互模式

```bash
uv run mewcode -p "解释这个项目的认证流程"
uv run mewcode -p "..." --output-format stream-json  # NDJSON 流式输出
```

### Remote 模式

```bash
uv run mewcode --remote
# 然后打开 http://localhost:18888
```

### 权限模式

| 模式 | 说明 |
|------|------|
| `default` | 执行命令前询问 |
| `accept-edits` | 自动批准文件编辑，命令仍需确认 |
| `plan` | 先提出变更方案，审核后执行 |
| `bypass` | 自动批准所有操作 |

在 TUI 中按 `Shift+Tab` 切换模式。

## 项目结构

```
mewcode/
├── app.py              # Textual TUI 主应用
├── agent.py            # 核心 Agent 循环
├── client.py           # LLM Provider 客户端（Anthropic、OpenAI）
├── config.py           # YAML 配置加载
├── conversation.py     # 对话状态管理
├── tools/              # 工具实现（Bash、读写编辑文件、Grep、Glob 等）
├── agents/             # 子 Agent 系统（加载器、任务管理、追踪）
├── teams/              # 多 Agent 团队协调
├── memory/             # 文件记忆与自动召回
├── skills/             # 可插拔技能系统
├── hooks/              # 事件钩子引擎
├── mcp/                # MCP 客户端与工具封装
├── permissions/        # 权限检查、沙箱、规则引擎
├── sandbox/            # OS 级沙箱（bwrap、seatbelt）
├── worktree/           # Git worktree 隔离
├── commands/           # 斜杠命令（/help、/clear、/memory 等）
└── filehistory/        # 文件修改追踪
```

## 依赖

- [Textual](https://github.com/Textualize/textual) — TUI 框架
- [Anthropic SDK](https://github.com/anthropics/anthropic-sdk-python) — Claude API 客户端
- [OpenAI SDK](https://github.com/openai/openai-python) — OpenAI API 客户端
- [MCP SDK](https://github.com/modelcontextprotocol/python-sdk) — Model Context Protocol
- [Pydantic](https://github.com/pydantic/pydantic) — 数据校验
- [PyYAML](https://github.com/yaml/pyyaml) — 配置文件解析

## License

MIT
