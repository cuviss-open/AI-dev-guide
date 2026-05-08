# AI 开发指南

AI Dev Guide 是一套可复用的 AI 协作开发规范。它为代码仓库提供清晰的控制层：AI 先读什么、哪些内容不能进入上下文、如何保持修改范围可审查、如何在收尾时验证结果。

## 语言文件约定

本仓库用文件名后缀区分语言：

- `*.zh.md`：中文。
- `*.en.md`：英文。

AI 开发者只读取一种语言：优先读取宿主项目 `AGENTS.md` 指定的语言；未指定时，使用用户当前对话语言。只有在翻译、校对或维护本指南时，才需要同时读取两种语言。

## 接入项目

```bash
git submodule add https://github.com/youyegit/AI-dev-guide.git AI-dev-guide
git submodule update --init --recursive
```

然后创建或更新宿主仓库根目录 `AGENTS.md`：

```md
# AGENTS.md

本项目继承 `AI-dev-guide/AGENTS.base.zh.md`。

AI 开发者必须先遵守通用规范，再遵守本项目规则。

若规则冲突，在本仓库内以项目根目录 `AGENTS.md` 为准。
```

## 仓库内容

```text
AI-dev-guide/
├── AGENTS.base.zh.md / AGENTS.base.en.md
├── README.md
├── docs/
│   └── README.zh.md / README.en.md
└── rules/
    ├── common/
    │   ├── code-maintenance.zh.md / code-maintenance.en.md
    │   ├── context-management.zh.md / context-management.en.md
    │   ├── repository-boundaries.zh.md / repository-boundaries.en.md
    │   └── security-context.zh.md / security-context.en.md
    └── languages/
        ├── rust.zh.md / rust.en.md
        ├── typescript-react.zh.md / typescript-react.en.md
        └── css.zh.md / css.en.md
```

宿主项目应自行编写根 `AGENTS.md`，并引用对应语言的通用基线规则。

## 设计目标

- 让 AI 修改保持小步、可审查、可回滚。
- 让人类和 AI 都清楚第一阅读路径。
- 防止密钥、token、真实 `.env`、缓存、日志和本地数据库进入 AI 上下文。
- 鼓励按领域拆分模块，避免巨型混合职责文件。
- 让每个开发任务都以具体验证结束。

## 宿主项目模式

宿主项目应维护项目专属真源，包括产品目标、当前阶段、源码结构、只读目录、构建测试命令和领域规则。

本指南维护通用过程真源，包括 AI 协作行为、上下文安全、通用模块边界、语言规则和完成报告方式。

## 许可证

Apache-2.0。
