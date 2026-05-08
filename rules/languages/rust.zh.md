# Rust 规则

## 核心原则

1. 一个文件只表达一个清晰领域。
2. 类型定义、纯逻辑、I/O 边界、错误处理、运行入口应尽量分离。
3. `lib.rs` / `mod.rs` 只做模块声明、re-export 和少量 crate 文档，不堆业务逻辑。
4. 不写万能 `utils`、`helpers`、`common`、`misc`。
5. AI 每次修改应只需要阅读 3-5 个相关文件。
6. 能用标准库解决的，不引入依赖；能写成纯 Rust 逻辑的，不依赖运行时或框架。

## 文件行数

- 普通 Rust 文件目标：300-500 行。
- 警戒线：600 行。
- 上限：1000 行。
- `mod.rs` 最多 150 行。
- `main.rs` 最多 100 行。

超过 600 行必须评估拆分；超过 1000 行默认不应作为完成状态交付，除非是生成代码或静态数据。早期 crate 可以从 `lib.rs` 起步，但一旦同一文件承载多类职责，就应拆分。

## 函数长度

- 单函数目标：30-60 行。
- 超过 80 行必须评估拆分。
- 超过 100 行默认禁止。

优先拆成参数准备、纯逻辑计算、I/O 调用、错误映射、状态写回等阶段。

## 推荐结构

```text
src/
  lib.rs
  main.rs
  config.rs
  error.rs
  domain/
    mod.rs
    model.rs
    logic.rs
  io/
    mod.rs
    fs.rs
    http.rs
  cli/
    mod.rs
    args.rs
```

- `domain/` 放领域类型和纯逻辑。
- `io/` 放文件、网络、进程、数据库等外部边界。
- `cli/`、`server/`、`worker/` 等入口目录只负责参数、路由或调度，不写核心业务。
- `config.rs` 负责配置结构、默认值和解析，不直接承载业务流程。
- `error.rs` 负责错误类型和映射，避免在各处散落字符串错误。

具体项目可以使用自己的目录名，但职责边界必须清晰。

## 依赖控制

新增任何依赖前必须评估：

- 标准库或已有依赖是否已经足够。
- 新依赖是否会拉入大量传递依赖、构建脚本、原生库或大体积二进制。
- 是否只需要依赖的一小部分能力。
- 是否会显著增加首次编译时间、增量编译时间或用户硬盘占用。
- 是否会引入不必要的默认 features。

对 `tokio`、`axum`、`reqwest`、`sqlx`、`openssl`、`rusqlite`、`bevy`、`tauri` 等较重依赖尤其谨慎。需要使用时优先关闭默认 features，并只开启当前任务需要的 features。例如：

```toml
tokio = { version = "1", default-features = false, features = ["rt", "macros", "time"] }
axum = { version = "0.7", default-features = false, features = ["tokio", "http1", "json"] }
reqwest = { version = "0.12", default-features = false, features = ["json", "rustls-tls"] }
```

不要因为方便就开启 `full`、`default` 或整套运行时能力。确实需要时，必须说明原因。

## Features 规则

- 新增 crate feature 时，命名必须表达能力边界，而不是临时任务名。
- 默认 features 应保持最小，避免把可选能力默认编译进所有用户机器。
- 平台特定、后端特定、数据库特定、UI 特定能力应做成可选 feature。
- 新增 feature 后，检查是否影响 workspace 其他 crate 的默认编译。
- 避免多个 feature 隐式开启同一批大依赖，必要时建立清晰的 feature 组合说明。

## AI 修改约束

1. 一次只改一个明确领域模块。
2. 修改前说明影响文件。
3. 不顺手重构无关模块。
4. 不新增 `common.rs`、`helpers.rs`、`misc.rs`、泛化 `utils.rs`。
5. 新增逻辑优先放入具体领域目录。
6. 公共类型、错误类型、配置 Schema 或跨模块 API 变更必须说明影响面。
7. 新增依赖或 feature 必须说明原因、替代方案和编译成本影响。
8. Rust 代码变更默认运行格式和编译检查。

## 命名

- 类型：语义名优先，如 `RunConfig`、`TaskState`、`ProviderClient`。
- 错误：`XxxError`。
- Trait：表达能力，如 `ConfigStore`、`MessageTransport`。
- 纯逻辑函数：`calculate_xxx`、`resolve_xxx`、`evaluate_xxx`。
- I/O 函数：动词加对象，如 `load_config`、`write_snapshot`、`fetch_catalog`。
- 测试函数：描述行为结果，不写空泛名称。

## 何时必须拆分

出现以下情况应拆分，或在任务输出中解释暂不拆分的原因：

- 一个文件同时包含配置、领域逻辑、I/O、入口调度、测试辅助中的三类及以上。
- 一个文件超过 600 行。
- 一个函数超过 80 行。
- 新需求只影响局部逻辑，却必须阅读整个巨型文件。
- 新依赖只服务一个小功能，却迫使整个 workspace 编译大量无关特性。

## 测试

纯逻辑优先补单元测试。I/O 边界优先用临时目录、mock transport、fixture 或集成测试覆盖。跨 crate 行为放到对应 crate 测试或 workspace 集成测试中。

新增 feature 时，应至少运行能覆盖默认 feature 和新增 feature 的检查命令。

## 完成前检查

Rust 代码变更默认执行：

```bash
cargo fmt
cargo check --workspace
```

涉及测试逻辑时继续执行：

```bash
cargo test --workspace
```

涉及依赖或 feature 变更时，优先运行能覆盖改动范围的精准检查：

```bash
cargo tree -e features -p <crate>
cargo check -p <crate> --no-default-features --features <feature>
cargo check -p <crate>
```

只有项目明确要求、成本可接受，或需要验证全量 feature 组合时，才运行 `cargo check --workspace --all-features`。

如果检查无法运行，说明命令、原因和风险。
