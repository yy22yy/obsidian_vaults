# Claude Code `.claude` 目录功能详解

> Claude Code 的全局配置目录，位于用户主目录下：`~/.claude/`（Windows 为 `C:\Users\用户名\.claude\`）

---

## 核心配置文件

| 文件 | 功能说明 |
|------|----------|
| `settings.json` | **主配置文件** - 存储 API 认证信息（Token、模型、Base URL） |
| `models.json` | **模型管理** - 记录可用模型列表、过期时间和优先级顺序 |
| `history.jsonl` | **全局历史** - 所有对话的 JSONL 格式记录 |

### settings.json 结构示例

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-xxx",
    "ANTHROPIC_MODEL": "glm-5",
    "ANTHROPIC_BASE_URL": "https://dashscope.aliyuncs.com/apps/anthropic"
  }
}
```

> 切换模型时修改 `ANTHROPIC_MODEL` 字段即可

### models.json 结构示例

```json
{
  "models": [
    {"name": "glm-5", "expire": "2026/05/18", "order": 9},
    {"name": "qwen3.6-plus", "expire": "2026/07/02", "order": 17}
  ]
}
```

---

## 数据缓存目录

| 目录 | 功能说明 |
|------|----------|
| `cache/` | 缓存 changelog 等元数据 |
| `paste-cache/` | 粘贴图片/文件的临时缓存 |
| `file-history/` | 文件编辑历史版本（可回滚） |
| `shell-snapshots/` | Shell 环境状态快照 |
| `downloads/` | Claude Code 下载的文件存放处 |

---

## 项目/会话管理

| 目录 | 功能说明 |
|------|----------|
| `projects/` | **核心目录** - 每个工作目录独立存储对话记录、记忆、计划 |
| `sessions/` | 会话元数据（会话 ID 映射） |
| `tasks/` | 任务列表数据 |
| `plans/` | Plan Mode 生成的计划文件 |

### projects 子目录结构

```
projects/
└── C--Users-86132/          # 项目目录名（路径编码）
    ├── *.jsonl              # 各会话的完整对话记录
    └── memory/              # 项目记忆文件
        ├── MEMORY.md        # 记忆索引
        └── *.md             # 各类型记忆文件
```

---

## 插件/扩展

| 目录 | 功能说明 |
|------|----------|
| `plugins/` | MCP 服务器插件配置（GitHub、Slack 等集成） |
| `ide/` | IDE 集成相关数据（VS Code、JetBrains） |

---

## 其他辅助目录

| 目录 | 功能说明 |
|------|----------|
| `backups/` | settings.json 自动备份（修改前备份） |
| `telemetry/` | 使用遥测/统计数据 |
| `debug/` | 调试日志文件 |

---

## 常用操作

### 切换模型
修改 `settings.json` 中的 `ANTHROPIC_MODEL` 值

### 查看对话历史
`history.jsonl` 或 `projects/项目名/*.jsonl`

### 清理缓存
可安全删除 `cache/`、`paste-cache/`、`downloads/`

### 重置配置
删除 `settings.json`，Claude Code 会重新生成

---

## 相关文件

- [[Claude Code]] - Claude Code 简介
- [[切换阿里云模型方法]] - 如何切换模型
- [[cc接入阿里云]] - 接入阿里云配置