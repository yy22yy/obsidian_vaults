# CC Switch - Claude Code 模型切换工具

> 快速切换 Claude Code 使用的 AI 模型，解决额度用完时的切换痛点

---

## 背景问题

在使用 Claude Code 时，经常会遇到：
- 模型额度用完，无法继续对话
- 需要频繁修改 `settings.json` 切换模型
- 多个模型需要手动记忆和输入

**CC Switch** 提供一键切换功能，快速在不同模型间切换。

---

## 方案对比

| 方案 | 优点 | 缺点 |
|------|------|------|
| 手动改 settings.json | 直接有效 | 路径长、易出错 |
| 别名命令 | 快速 | 需要记忆命令 |
| **CC Switch（推荐）** | 交互式选择、可视化 | 需要额外配置 |

---

## 方案一：PowerShell 函数（Windows）

### 1. 添加到 PowerShell 配置文件

```powershell
# 打开配置文件
notepad $PROFILE
```

### 2. 添加以下代码

```powershell
# CC Switch - Claude Code 模型切换
function al_sm {
    param(
        [string]$Model = "",
        [switch]$List
    )
    
    $settingsPath = "$env:USERPROFILE\.claude\settings.json"
    $modelsPath = "$env:USERPROFILE\.claude\models.json"
    
    # 显示模型列表
    if ($List) {
        if (Test-Path $modelsPath) {
            $models = Get-Content $modelsPath | ConvertFrom-Json
            Write-Host "可用模型列表：" -ForegroundColor Green
            $models.models | Sort-Object expire | Format-Table -AutoSize
        } else {
            Write-Host "未找到模型列表文件" -ForegroundColor Red
        }
        return
    }
    
    # 如果未指定模型，显示交互式选择
    if ($Model -eq "") {
        Write-Host "=== CC Switch - 模型切换 ===" -ForegroundColor Cyan
        Write-Host ""
        
        if (Test-Path $modelsPath) {
            $models = Get-Content $modelsPath | ConvertFrom-Json
            $index = 1
            foreach ($m in ($models.models | Sort-Object expire)) {
                $expireDate = [DateTime]::Parse($m.expire)
                $daysLeft = ($expireDate - (Get-Date)).Days
                $status = if ($daysLeft -gt 0) { "可用 (${daysLeft}天)" } else { "已过期" }
                Write-Host "[$index] $($m.name) - $status"
                $index++
            }
        }
        
        Write-Host ""
        Write-Host "[c] 自定义模型名"
        Write-Host "[q] 取消"
        Write-Host ""
        
        $choice = Read-Host "请选择"
        
        if ($choice -eq "q") { return }
        if ($choice -eq "c") {
            $Model = Read-Host "请输入模型名"
        } else {
            $idx = [int]$choice - 1
            $modelsList = $models.models | Sort-Object expire
            $Model = $modelsList[$idx].name
        }
    }
    
    # 执行切换
    $config = @"
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "",
    "ANTHROPIC_BASE_URL": "https://dashscope.aliyuncs.com/apps/anthropic",
    "ANTHROPIC_MODEL": "$Model"
  }
}
"@
    
    $config | Out-File -Encoding utf8 $settingsPath
    Write-Host "已切换至模型: $Model" -ForegroundColor Green
    Write-Host "请重启 Claude Code 生效" -ForegroundColor Yellow
}

# 别名设置
Set-Alias -Name sm -Value al_sm
Set-Alias -Name ml -Value al_sm -Option AllScope
function ml { al_sm -List }
```

### 3. 使用方法

```powershell
# 交互式选择模型
al_sm
# 或
sm

# 直接指定模型
al_sm "qwen3-max-2026-01-23"

# 查看模型列表
ml

# 查看带过期时间的列表
al_sm -List
```

---

## 方案二：Bash 脚本（Mac/Linux）

### 1. 创建脚本文件

```bash
# 创建脚本
cat > ~/.local/bin/cc-switch << 'EOF'
#!/bin/bash

SETTINGS_PATH="$HOME/.claude/settings.json"
MODELS_PATH="$HOME/.claude/models.json"

# 显示帮助
show_help() {
    echo "CC Switch - Claude Code 模型切换工具"
    echo ""
    echo "用法:"
    echo "  cc-switch              交互式选择模型"
    echo "  cc-switch <model>      直接切换到指定模型"
    echo "  cc-switch -l           列出所有模型"
    echo "  cc-switch -h           显示帮助"
}

# 列出模型
list_models() {
    if [ -f "$MODELS_PATH" ]; then
        echo "可用模型列表："
        cat "$MODELS_PATH" | jq -r '.models | sort_by(.expire) | .[] | "\(.name) (过期: \(.expire))"'
    else
        echo "未找到模型列表文件"
    fi
}

# 主逻辑
if [ "$1" = "-h" ] || [ "$1" = "--help" ]; then
    show_help
    exit 0
fi

if [ "$1" = "-l" ] || [ "$1" = "--list" ]; then
    list_models
    exit 0
fi

# 交互式选择
if [ -z "$1" ]; then
    echo "=== CC Switch - 模型切换 ==="
    echo ""
    
    if [ -f "$MODELS_PATH" ]; then
        models=$(cat "$MODELS_PATH" | jq -r '.models | sort_by(.expire) | .[] | "\(.name)|\(.expire)"')
        IFS=$'\n' read -rd '' -a model_array <<< "$models"
        
        idx=1
        for model_info in "${model_array[@]}"; do
            IFS='|' read -r name expire <<< "$model_info"
            expire_ts=$(date -d "$expire" +%s 2>/dev/null || date -j -f "%Y/%m/%d" "$expire" +%s)
            now_ts=$(date +%s)
            days_left=$(( (expire_ts - now_ts) / 86400 ))
            
            if [ $days_left -gt 0 ]; then
                status="可用 (${days_left}天)"
            else
                status="已过期"
            fi
            echo "[$idx] $name - $status"
            ((idx++))
        done
    fi
    
    echo ""
    echo "[c] 自定义模型名"
    echo "[q] 取消"
    echo ""
    
    read -p "请选择: " choice
    
    if [ "$choice" = "q" ]; then exit 0; fi
    
    if [ "$choice" = "c" ]; then
        read -p "请输入模型名: " MODEL
    else
        MODEL=$(echo "$models" | sed -n "${choice}p" | cut -d'|' -f1)
    fi
else
    MODEL="$1"
fi

# 执行切换
cat > "$SETTINGS_PATH" << EOFCFG
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "",
    "ANTHROPIC_BASE_URL": "https://dashscope.aliyuncs.com/apps/anthropic",
    "ANTHROPIC_MODEL": "$MODEL"
  }
}
EOFCFG

echo "已切换至模型: $MODEL"
echo "请重启 Claude Code 生效"
EOF

# 添加执行权限
chmod +x ~/.local/bin/cc-switch

# 添加到 PATH
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
```

### 2. 添加别名

```bash
# 添加到 ~/.bashrc
echo 'alias sm="cc-switch"' >> ~/.bashrc
echo 'alias ml="cc-switch -l"' >> ~/.bashrc

source ~/.bashrc
```

---

## 方案三：直接命令（极简版）

### Windows (PowerShell)

```powershell
# 创建快捷函数
function Switch-Model {
    param([Parameter(Mandatory=$true)]$m)
    @"{"env":{"ANTHROPIC_AUTH_TOKEN":"","ANTHROPIC_BASE_URL":"https://dashscope.aliyuncs.com/apps/anthropic","ANTHROPIC_MODEL":"$m"}}"@ | Out-File "$env:USERPROFILE\.claude\settings.json" -Encoding utf8
    Write-Host "已切换至: $m" -ForegroundColor Green
}
Set-Alias -Name m1 -Value Switch-Model

# 使用：m1 "模型名"
m1 "qwen3-max-2026-01-23"
m1 "MiniMax-M2.1"
```

### Mac/Linux (Bash)

```bash
# 添加到 ~/.bashrc
m1() {
    echo "{\"env\":{\"ANTHROPIC_AUTH_TOKEN\":\"\",\"ANTHROPIC_BASE_URL\":\"https://dashscope.aliyuncs.com/apps/anthropic\",\"ANTHROPIC_MODEL\":\"$1\"}}" > ~/.claude/settings.json
    echo "已切换至: $1"
}

# 使用
m1 "qwen3-max-2026-01-23"
```

---

## 使用流程

```
1. 额度用完 → Claude Code 停止响应
   ↓
2. 终端输入：sm 或 al_sm
   ↓
3. 选择/输入新模型
   ↓
4. 重启 Claude Code
   ↓
5. 继续工作
```

---

## 注意事项

1. **切换后需要重启 Claude Code** - 配置不会热加载
2. **模型名需准确** - 建议使用 models.json 中的标准名称
3. **保留 key 和 URL** - 只修改 model 字段
4. **过期检查** - 脚本会显示模型剩余天数

---

## 相关笔记

- [[切换阿里云模型方法]] - 手动切换方法
- [[cc接入阿里云]] - 阿里云配置详解
- [[claude 目录功能详解]] - settings.json 位置

---

## 参考

- [Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code/overview)
- [阿里云 DashScope](https://dashscope.aliyun.com/)
