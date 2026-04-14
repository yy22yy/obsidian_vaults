

## PowerShell Profile 文件详解

### 本质是什么

Profile 就是一个**普通的 `.ps1` 脚本文件**，没有任何特殊格式。它的唯一特殊之处在于：

> PowerShell 每次启动时，会**自动执行**这个文件。

你可以把它理解成 PowerShell 的"开机自启动脚本"。

---

### 文件在哪里

```powershell
# 查看你的 Profile 路径
echo $PROFILE
```

一般输出类似：

```
C:\Users\86132\Documents\PowerShell\Microsoft.PowerShell_profile.ps1
```

PowerShell 实际上有**4个** Profile，优先级从高到低：

```
当前用户 + 当前主机   ← $PROFILE（最常用，平时配这个就够）
当前用户 + 所有主机
所有用户 + 当前主机
所有用户 + 所有主机
```

平时只需要管 `$PROFILE` 这一个。

---

### 生效原理

```
PowerShell 启动
      ↓
检查 $PROFILE 路径是否存在
      ↓
存在 → 从头到尾执行一遍脚本内容
      ↓
执行完毕 → 进入你看到的交互终端
```

所以 Profile 里写的所有内容，**等价于你每次开终端手动输入一遍**。这也解释了：

- 为什么别名要写进 Profile 才能永久生效
- 为什么改了 Profile 要重启终端才能看到效果（或者用 `. $PROFILE` 立即重载）

---

### 配置要求

本质是 `.ps1` 脚本，所以 **PowerShell 能写的它都能写**，没有特殊语法要求。常见的几类配置：

```powershell
# ① 别名 —— 简单的命令映射，只能是命令到命令
Set-Alias py python
Set-Alias g git

# ② 函数 —— 有逻辑、有参数的操作必须用 function
function which($cmd) {
    (Get-Command $cmd).Source
}

function activate {
    .venv\Scripts\Activate.ps1
}

# ③ 环境变量
$env:HTTP_PROXY = "http://127.0.0.1:7890"

# ④ 直接执行任意命令（每次开终端都会跑）
clear                          # 启动时清屏
echo "Hello, Vango"            # 启动时打招呼
conda activate base            # 启动时激活 conda 环境
```

---

### Set-Alias 和 function 的区别

这是初学者最容易混淆的地方：

||`Set-Alias`|`function`|
|---|---|---|
|用途|命令 → 命令的简单映射|可以包含逻辑、参数、多行操作|
|能传参数吗|❌ 不能|✅ 可以|
|例子|`Set-Alias py python`|`function which($cmd){...}`|

```powershell
# 错误示范：Set-Alias 不能带参数
Set-Alias ll "ls -l"       # ❌ 这样写无效

# 正确做法：带参数的要用 function
function ll { ls -l $args }   # ✅
```

---

### 立即重载（不用重启终端）

```powershell
. $PROFILE
```

注意前面有个点和空格，这是 PowerShell 的"点源执行"语法，意思是在当前会话里执行脚本，变量和函数都会保留在当前环境中。