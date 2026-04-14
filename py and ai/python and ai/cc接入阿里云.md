---
tags: [tools, claude-code, agentic-cli, 百炼, qwen, windows]
date: 2026-04-14
status: done
---
[[切换阿里云模型方法]]

# Claude Code 接入阿里云百炼 · 完整踩坑记录

## 目标

在 Windows 本地安装 Claude Code，绕过国内对 `api.anthropic.com` 的封锁，
接入阿里云百炼的 Anthropic API 兼容接口，使用通义千问系列模型。

---

## 最终配置（可直接复用）

### 文件：`~/.claude/settings.json`

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "你的百炼API_KEY",
    "ANTHROPIC_BASE_URL": "https://dashscope.aliyuncs.com/apps/anthropic",
    "ANTHROPIC_MODEL": "qwen3.5-plus"
  }
}
```

写入命令（PowerShell）：

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude"

@"
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "你的百炼API_KEY",
    "ANTHROPIC_BASE_URL": "https://dashscope.aliyuncs.com/apps/anthropic",
    "ANTHROPIC_MODEL": "qwen3.5-plus"
  }
}
"@ | Out-File -Encoding utf8 "$env:USERPROFILE\.claude\settings.json"
```

> 用 `ANTHROPIC_AUTH_TOKEN` 而非 `ANTHROPIC_API_KEY`，百炼推荐前者。
> 创建一个claude的配置文件并且写入

---

## 踩坑全记录

### 坑 1：直接运行 `claude` 报网络错误

**报错：**
```
Unable to connect to Anthropic services
Failed to connect to api.anthropic.com: ERR_BAD_REQUEST
Assertion failed: !(handle->flags & UV_HANDLE_CLOSING)
```

**原因：**
- Claude Code 默认连接 `api.anthropic.com`，国内封锁
- `Assertion failed` 是连接失败引发的 Windows libuv 进程崩溃，非独立 bug

**解决：** 配置百炼的 Anthropic 兼容接口作为 Base URL

---

### 坑 2：`setx` 设置变量后仍然报同样的错

**原因：**
`setx` 设置的环境变量只在**新开的终端窗口**生效，当前窗口不刷新。

**排查方法：**
```powershell
echo $env:ANTHROPIC_BASE_URL   # 空 = 变量未生效
```

**解决：** 关闭当前窗口，重开一个新 PowerShell

---

### 坑 3：变量设对了，Claude Code 仍然读不到

**原因：**
Claude Code 对 `ANTHROPIC_API_KEY` 字段读取不稳定，
且环境变量方式容易被其他层覆盖。

**解决：** 改用 `~/.claude/settings.json` 配置文件，
Claude Code 优先读取该文件，完全绕开环境变量。

---

### 坑 4：Auth conflict 警告反复出现

**报错：**
```
Auth conflict: Both a token (ANTHROPIC_AUTH_TOKEN) and an API key
(ANTHROPIC_API_KEY) are set.
```

**原因分析：**
同时存在多个认证来源，Claude Code 不知道用哪个：

| 来源 | 内容 |
|---|---|
| `settings.json` | `ANTHROPIC_AUTH_TOKEN`（新配置）|
| 环境变量 Process 层 | `ANTHROPIC_API_KEY`（本次窗口残留）|
| 环境变量 User/Machine 层 | `ANTHROPIC_API_KEY`（`setx` 永久写入）|
| `~/.claude.json` | `customApiKeyResponses.approved`（登录时缓存）|

**逐层清除方法：**

```powershell
# 清 User 和 Machine 层（setx 写入的）
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", $null, "User")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", $null, "Machine")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", $null, "User")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", $null, "Machine")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_MODEL", $null, "User")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_MODEL", $null, "Machine")

# 清 Process 层（当前窗口）
Remove-Item Env:ANTHROPIC_API_KEY -ErrorAction SilentlyContinue

# 清 ~/.claude.json 里缓存的旧 Key
$json = Get-Content "$env:USERPROFILE\.claude.json" -Raw | ConvertFrom-Json
$json.customApiKeyResponses.approved = @()
$json | ConvertTo-Json -Depth 10 | Out-File -Encoding utf8 "$env:USERPROFILE\.claude.json"
```

**验证清干净：**
```powershell
[System.Environment]::GetEnvironmentVariable("ANTHROPIC_API_KEY", "User")
[System.Environment]::GetEnvironmentVariable("ANTHROPIC_API_KEY", "Machine")
# 两行都输出空才算干净
```

---

### 坑 5：403 invalid api-key

**原因：**
对话中不小心暴露了 API Key 明文，Key 被手动撤销后，
settings.json 里还存着旧 Key，导致每次请求都 403。

**教训：** 永远不要在任何对话/日志/截图中暴露完整 API Key。

**解决：**
1. 去百炼控制台删除旧 Key，生成新 Key
2. 更新 settings.json 里的 `ANTHROPIC_AUTH_TOKEN` 字段

---

### 坑 6：PowerShell 的 `curl` 不是真正的 curl

Windows PowerShell 里 `curl` 是 `Invoke-WebRequest` 的别名，
`-H` 参数语法完全不同，直接用 Linux curl 命令会报错。

**正确的 API 连通性测试方法：**
```powershell
Invoke-RestMethod `
  -Uri "https://dashscope.aliyuncs.com/apps/anthropic/v1/messages" `
  -Method POST `
  -Headers @{
    "x-api-key" = "你的Key"
    "anthropic-version" = "2023-06-01"
    "content-type" = "application/json"
  } `
  -Body '{"model":"qwen3.5-plus","max_tokens":100,"messages":[{"role":"user","content":"hello"}]}'
```

---

## 环境变量优先级（Claude Code 读取顺序）

```
settings.json env 字段          ← 最高优先级，推荐唯一配置来源
    ↓
Process 层环境变量（当前窗口）
    ↓
User 层环境变量（setx 用户级）
    ↓
Machine 层环境变量（系统级）    ← 最低优先级
```

**原则：只用 settings.json，不用 setx，避免多层冲突。**

---

## 百炼接口参数

| 参数 | 值 |
|---|---|
| Base URL（国内北京） | `https://dashscope.aliyuncs.com/apps/anthropic` |
| Base URL（国际新加坡） | `https://dashscope-intl.aliyuncs.com/apps/anthropic` |
| API Key 地域 | 国内版必须用北京地域 Key |
| 认证字段 | `ANTHROPIC_AUTH_TOKEN`（推荐）|

### 可用模型

| 模型 | 适用场景 |
|---|---|
| `qwen3-coder-plus` | coding 专用，首选 |
| `qwen3.5-plus` | 综合均衡 |
| `qwen3-max` | 复杂推理任务 |
| `qwen-flash` | 轻任务省 Token |

---

## API Key 安全管理

### 方案 A：PowerShell Profile（日常最简）
```powershell
notepad $PROFILE
```
写入：
```powershell
$env:ANTHROPIC_AUTH_TOKEN = "your_key"
$env:ANTHROPIC_BASE_URL = "https://dashscope.aliyuncs.com/apps/anthropic"
$env:ANTHROPIC_MODEL = "qwen3-coder-plus"
```

### 方案 B：Windows 凭据管理器（可上传 dotfiles）
```powershell
Install-Module -Name CredentialManager -Scope CurrentUser
New-StoredCredential -Target "bailian_api_key" -UserName "apikey" `
  -SecurePassword (Read-Host -AsSecureString "输入Key") -Persist LocalMachine
```
Profile 读取：
```powershell
$cred = Get-StoredCredential -Target "bailian_api_key"
$env:ANTHROPIC_AUTH_TOKEN = $cred.GetNetworkCredential().Password
```

---

## 免费额度快到期怎么办

- 百炼新用户额度有效期 **90 天**，到期不可延期
- **学生渠道**：阿里云「云工开物」→ 学信网认证 → 每年 **300元无门槛代金券**
  - 入口：`university.aliyun.com`
- **其他免费平台**（均支持 Anthropic 格式，只换 Base URL）：

| 平台 | Base URL | 免费内容 |
|---|---|---|
| 魔搭社区 | `https://api-inference.modelscope.cn/v1` | Qwen3-Coder 500次 |
| DeepSeek | `https://api.deepseek.com/anthropic` | 新用户赠额 |
| Kimi | `https://api.moonshot.cn/anthropic` | 新用户赠额 |
| 智谱 GLM | `https://open.bigmodel.cn/api/anthropic` | 新用户赠额 |

---

## 相关链接

- 百炼控制台：https://bailian.console.aliyun.com
- 百炼 Claude Code 文档：https://help.aliyun.com/zh/model-studio/claude-code
- 云工开物高校计划：https://university.aliyun.com
- Claude Code 官方文档：https://code.claude.com/docs