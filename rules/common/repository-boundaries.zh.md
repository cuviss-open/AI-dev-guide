# 仓库边界规则

## 推荐边界

- 根 `AGENTS.md`：协作规则和项目专属 AI 规则。
- 根 `README.md`：公开用户和贡献者入口。
- 根脚本：用户可从仓库根目录运行的稳定入口。
- `src/`、`app/`、`packages/` 或项目定义目录：应用代码。
- `docs/`：用户文档。
- `rules/common/`：通用编码和维护规则。
- `rules/languages/`：特定语言和样式规则。
- 子模块：拥有独立历史和边界的外部仓库。

## 子模块

子模块应视为独立仓库。

- 除非任务明确针对子模块，否则不要修改子模块。
- 修改子模块时，检查并报告子模块自己的工作区状态。
- 获得用户明确提交许可后，先在子模块内提交，再让父仓库记录新的子模块指针。
- 父仓库应记录预期的子模块提交。

## 生成目录和依赖目录

不要手工编辑依赖或生成目录，例如：

- `node_modules/`
- `dist/`、`build/`、`target/`、`.next/`、`.turbo/`
- 生成的 SDK 或资产，除非项目明确跟踪

需要更新时使用宿主项目的生成命令。

## 高破坏性命令

以下命令可能严重伤害项目，必须严格限制。除非用户明确要求并说明目标，否则不得执行：

- 删除或批量移动文件：`rm`、`rm -r`、`rm -rf`、`mv`、批量 `find -delete`。
- 丢弃或覆盖改动：`git checkout -- <path>`、`git restore`、`git reset --hard`。
- 清理未跟踪文件：`git clean`、`git clean -fd`、`git clean -fdx`。
- 重写历史或远端状态：`git rebase`、`git commit --amend`、`git push --force`、`git push --force-with-lease`。
- 直接操作 `.git/` 目录或删除子模块目录。

获得许可后，也应先运行只读检查，例如 `git status --short`、`git diff --name-only` 或目标路径列表，确认影响范围再执行。
