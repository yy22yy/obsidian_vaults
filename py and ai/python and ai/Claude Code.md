[[cc接入阿里云]]
[[它的简介]]

[[Claude Code适用人群]]

[[cc命令速查]]


- 本质：一个本地终端运行的AI Agent
- function：read and write codebase、change files、

- 阿里云接入web链接：
[Claude Code-大模型服务平台百炼(Model Studio)-阿里云帮助中心](https://help.aliyun.com/zh/model-studio/claude-code)



## 首次启动被墙住

这是个典型的网络封锁报错——你还没设置百炼的环境变量，Claude Code 还在尝试直连 `api.anthropic.com`，国内被墙了。

按我上面给的步骤，在 **新的 PowerShell 窗口**里先设置环境变量：

```powershell
$env:ANTHROPIC_BASE_URL = "https://dashscope.aliyuncs.com/apps/anthropic"
$env:ANTHROPIC_API_KEY = "你的百炼API_KEY"
$env:ANTHROPIC_MODEL = "qwen3-coder-plus"
```

然后再运行 `claude`。

还没拿到百炼 API Key 的话，先去这里申请：[bailian.console.aliyun.com](https://bailian.console.aliyun.com/) → 右上角 → API Key 管理 → 新建（选**北京地域**）。

---


 `Assertion failed: !(handle->flags & UV_HANDLE_CLOSING)` 是 Windows 上 libuv 的崩溃，通常是因为连接失败后进程异常退出导致的，换了国内 Base URL 之后这个问题也会消失，不用单独处理。、


## 阿里云接入命令

```shell
setx ANTHROPIC_API_KEY ""
setx ANTHROPIC_BASE_URL "https://dashscope.aliyuncs.com/apps/anthropic"
setx ANTHROPIC_MODEL "qwen3-max-2026-01-23"
```


### 运行结果保存

```shell
(base) C:\Users\86132 [master ≡ +167 ~0 -0 !]> setx ANTHROPIC_API_KEY ""

SUCCESS: Specified value was saved.
(base) C:\Users\86132 [master ≡ +167 ~0 -0 !]> setx ANTHROPIC_BASE_URL "https://dashscope.aliyuncs.com/apps/anthropic"

SUCCESS: Specified value was saved.
(base) C:\Users\86132 [master ≡ +167 ~0 -0 !]> setx ANTHROPIC_MODEL "qwen3-max-2026-01-23"

SUCCESS: Specified value was saved.
```

### 输入claude后终端反馈
```shell
 Unable to connect to Anthropic services

 Failed to connect to api.anthropic.com: ERR_BAD_REQUEST

 Please check your internet connection and network settings.

 Note: Claude Code might not be available in your country. Check supported countries at
 https://anthropic.com/supported-countries
```