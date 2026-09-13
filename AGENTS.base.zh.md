# AGENTS.base.zh.md

宿主项目在根 `AGENTS.md` 中引用本文件，并补充项目专属规则。若冲突，以宿主项目根 `AGENTS.md` 为准。

## 语言规则

- 中文文件使用 `*.zh.md`，英文文件使用 `*.en.md`。
- AI 只读取一种语言：优先读取宿主项目指定语言；未指定时，使用用户当前对话语言。
- 修改 `AI-dev-guide/` 的通用规则、README 或公开说明时，必须同步修改对应的中文和英文版本。
- 根 `README.md` 是双语入口例外；正式说明放在 `docs/README.zh.md` 和 `docs/README.en.md`。

## 读取顺序

1. 宿主项目根 `AGENTS.md`。
2. `AI-dev-guide/AGENTS.base.zh.md`。
3. `AI-dev-guide/rules/common/*.zh.md`。
4. 宿主项目公开入口文档，通常是 `README.md`。
5. 与任务直接相关的最小文件集合。
6. 宿主项目引用的其他项目专属规则。

只有任务涉及对应代码或样式修改时，才读取：

- Rust：`AI-dev-guide/rules/languages/rust.zh.md`
- TypeScript + React（含 `.tsx` 组件）：`AI-dev-guide/rules/languages/typescript-react.zh.md`
- CSS：`AI-dev-guide/rules/languages/css.zh.md`
- 编写或重写自动化脚本：`AI-dev-guide/rules/scripting.zh.md`

## Git 授权

- `rm`、`git checkout -- <path>`、`git checkout .`、`git restore`、`git reset`、`git clean`、强推、重写历史、直接操作 `.git/` 等命令属于高危操作；除非用户明确点名允许，否则一律不得执行。
- 未经用户明确允许，不得执行 `git commit` 或 `git push`。
- 未经用户明确允许，不得执行会删除、覆盖、重写或大规模移动项目文件的高破坏性命令。
- 未经用户明确允许，不得重写历史、丢弃用户改动、清理未跟踪文件、强推或重置工作区。
- 获得提交许可后，提交前检查工作区，确认 diff 只包含任务相关文件，并避免提交密钥、缓存、日志、本地数据库、依赖目录和构建产物。

## 内容质量

- 文档、代码注释和界面文案只写对使用者有行动价值的信息。
- 不写解释文件自身存在、平台常识、显而易见结构或当前改动原因的自解释内容。
- 不用“本文件用于”“本规范旨在”“这里说明”这类空泛开头；直接写规则、入口、命令或约束。
- 需要说明原因时，只保留会影响使用、维护、安全或决策的原因。

## 完成报告

```text
完成内容：
- ...

修改文件：
- ...

验证：
- ...

已知风险：
- ...

下一步：
- ...
```
