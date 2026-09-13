# 自动化脚本规范

## 语言选型

- 长期维护、跨平台运行,或含复杂逻辑(条件分支、配置解析、文件操作、错误处理)的脚本,默认使用 Python。
- 几十行以内、仅服务 Unix 的一次性简单操作,可用 Bash。
- 仅操作 Windows 系统能力的简单脚本,可用 PowerShell。
- 负责初始化尚未安装 Python/uv 的机器时,可保留极薄的 `bootstrap.sh` / `bootstrap.ps1`,仅准备运行环境;业务逻辑仍放 Python。
- 脚本一旦触及上述复杂度或预计长期维护,改用 Python。

## 既有子系统豁免

- 本规范约束新脚本与主动重写。
- 已成体系、有既定契约(如 `install` / `apply` / `check` 等入口)的 shell 子系统保持原语言;不为合规而重写可用代码。迁移须作为独立任务明确立项,而非顺带改写。

## Python 约定

- 运行与依赖管理用 uv,入口形式 `uv run path/to/script.py`。
- 无明确必要不增加第三方依赖;优先标准库(`pathlib`、`subprocess`、`shutil`、`os`、`json`、`platform`、`tempfile` 等)。
- 单文件脚本确需一个第三方依赖时,用 PEP 723 内联元数据(`# /// script`)声明,不新增 `requirements.txt` 等依赖文件。
- 文件、目录、路径操作用 `pathlib`,不手工拼接分隔符,不依赖特定平台路径格式。
- 优先兼容 macOS、Linux、WSL、Windows,避免无必要的平台绑定实现。
- 平台差异用 `platform`、`os.name`、`shutil.which()` 检测,不假设当前运行环境。

## 外部命令

- 调用 `git`、`npm`、`cargo`、`docker` 等 CLI 用 `subprocess` 参数数组,默认不用 `shell=True`。
  - 正:`subprocess.run(["git", "status"], check=True)`
  - 反:`subprocess.run("git status", shell=True)`
- 显式检查退出状态;失败保留明确错误信息和非零退出码,不静默忽略。

## 幂等

- 脚本尽量支持重复执行并保持幂等;已完成的操作再次执行不应报错或产生重复副作用。

## 结构

较大脚本按职责拆分,把平台探测、命令执行、配置解析收进 `lib/`,入口保持薄:

```
scripts/
├── check.py
├── update.py
└── lib/
    ├── platform.py
    ├── shell.py
    └── config.py
```

文件名按项目动词命名即可,不强制固定名(避免用 `setup.py`,它有 setuptools 语义)。
