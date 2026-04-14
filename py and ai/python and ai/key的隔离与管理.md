
---

## 方案一：写入 PowerShell Profile（最简单）

相当于每次开终端自动加载，一劳永逸。

```powershell
# 打开你的 profile 文件
notepad $PROFILE
```

在文件里加入：

```powershell
# AI CLI Keys
$env:ANTHROPIC_API_KEY = "sk-xxx你的百炼key"
$env:ANTHROPIC_BASE_URL = "https://dashscope.aliyuncs.com/apps/anthropic"
$env:ANTHROPIC_MODEL = "qwen3-coder-plus"

# 其他 key 也可以放这里
# $env:OPENAI_API_KEY = "sk-xxx"
```

保存后，新开的每个 PowerShell 窗口都会自动生效。

> ⚠️ 缺点：明文存在 `Documents\PowerShell\Microsoft.PowerShell_profile.ps1`，别人拿到你电脑能看到。

---

## 方案二：Windows 凭据管理器 + Profile 读取（安全版）

把 key 存进系统加密的凭据库，Profile 里只写读取逻辑。

**第一步：存入凭据**

```powershell
# 安装工具（只需一次）
Install-Module -Name CredentialManager -Scope CurrentUser

# 存储 key（弹出输入框，不会明文显示）
New-StoredCredential -Target "bailian_api_key" -UserName "apikey" -SecurePassword (Read-Host -AsSecureString "输入百炼API Key") -Persist LocalMachine
```

**第二步：Profile 里读取**

```powershell
# 写入 $PROFILE
$cred = Get-StoredCredential -Target "bailian_api_key"
$env:ANTHROPIC_API_KEY = $cred.GetNetworkCredential().Password
$env:ANTHROPIC_BASE_URL = "https://dashscope.aliyuncs.com/apps/anthropic"
$env:ANTHROPIC_MODEL = "qwen3-coder-plus"
```

Key 加密存在系统里，Profile 文件本身不含任何敏感内容，可以放心同步到 GitHub。

---

## 方案三：`.env` 文件 + 手动加载（项目隔离）

适合你不同项目用不同 key 的场景（比如毕设项目单独一套）。

在项目目录建 `.env`：

```
ANTHROPIC_API_KEY=sk-xxx
ANTHROPIC_BASE_URL=https://dashscope.aliyuncs.com/apps/anthropic
ANTHROPIC_MODEL=qwen3-coder-plus
```

Profile 里加一个加载函数：

```powershell
function Load-Env {
    Get-Content .env | ForEach-Object {
        if ($_ -match "^([^#][^=]+)=(.+)$") {
            [System.Environment]::SetEnvironmentVariable($matches[1].Trim(), $matches[2].Trim(), "Process")
        }
    }
    Write-Host ".env loaded" -ForegroundColor Green
}
```

用的时候在项目目录里运行 `Load-Env` 即可。记得把 `.env` 加到 `.gitignore`。

---

## 推荐选择

|场景|推荐方案|
|---|---|
|日常最省事|方案一（Profile 明文）|
|想上传 dotfiles 到 GitHub|方案二（凭据管理器）|
|多项目不同 key|方案三（.env 文件）|

你现在毕设阶段，**方案一**最直接。等之后有多个平台 key 需要管理，再升级到方案二或三。