

```shell
.venv/
├── Scripts/          ← Windows 下的可执行文件（Linux 叫 bin/）
│   ├── python.exe        ← 这个环境专属的解释器
│   ├── pip.exe           ← 这个环境专属的 pip
│   ├── Activate.ps1      ← PowerShell 激活脚本
│   ├── activate          ← bash 激活脚本（Linux 用）
│   └── activate.bat      ← cmd 激活脚本
│
├── Lib/
│   └── site-packages/    ← 你装的所有第三方包都在这里
│       ├── requests/         （举例）
│       └── flask/            （举例）
│
├── Include/          ← C 扩展头文件，编译某些包时用，平时不用管
│
└── pyvenv.cfg        ← 配置文件，记录了基础解释器路径
```

