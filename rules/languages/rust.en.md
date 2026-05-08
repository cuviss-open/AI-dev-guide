# Rust Rules

## Core Principles

1. One file should express one clear domain.
2. Type definitions, pure logic, I/O boundaries, error handling, and runtime entry points should be separated where practical.
3. `lib.rs` / `mod.rs` should contain module declarations, re-exports, and small crate docs only; do not pile business logic there.
4. Do not create catch-all `utils`, `helpers`, `common`, or `misc` modules.
5. Each AI change should require reading only 3-5 relevant files.
6. If the standard library is enough, do not add a dependency; if logic can be pure Rust, do not depend on a runtime or framework.

## File Size

- Normal Rust file target: 300-500 lines.
- Warning line: 600 lines.
- Upper limit: 1000 lines.
- `mod.rs`: at most 150 lines.
- `main.rs`: at most 100 lines.

Files above 600 lines must be evaluated for splitting; files above 1000 lines should not be delivered as complete by default unless they are generated code or static data. A young crate may start from `lib.rs`, but once one file carries multiple responsibilities, split it.

## Function Size

- Single-function target: 30-60 lines.
- Above 80 lines: evaluate splitting.
- Above 100 lines: disallowed by default.

Prefer splitting into parameter preparation, pure calculation, I/O calls, error mapping, and state write-back.

## Recommended Structure

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

- `domain/` contains domain types and pure logic.
- `io/` contains external boundaries such as files, networking, processes, and databases.
- `cli/`, `server/`, `worker/`, and similar entry directories handle arguments, routing, or scheduling, not core business logic.
- `config.rs` owns config structures, defaults, and parsing, but should not carry business flow.
- `error.rs` owns error types and mappings, avoiding scattered string errors.

Projects may use their own directory names, but responsibilities must remain clear.

## Dependency Control

Before adding any dependency, evaluate:

- Whether the standard library or existing dependencies are enough.
- Whether the new dependency pulls many transitive dependencies, build scripts, native libraries, or large binaries.
- Whether only a small part of the dependency is needed.
- Whether it significantly increases first compile time, incremental compile time, or user disk usage.
- Whether it enables unnecessary default features.

Be especially careful with heavier dependencies such as `tokio`, `axum`, `reqwest`, `sqlx`, `openssl`, `rusqlite`, `bevy`, and `tauri`. When they are needed, prefer disabling default features and enabling only the features required by the current task. For example:

```toml
tokio = { version = "1", default-features = false, features = ["rt", "macros", "time"] }
axum = { version = "0.7", default-features = false, features = ["tokio", "http1", "json"] }
reqwest = { version = "0.12", default-features = false, features = ["json", "rustls-tls"] }
```

Do not enable `full`, `default`, or entire runtime capability sets just for convenience. If they are truly needed, explain why.

## Feature Rules

- New crate features must describe capability boundaries, not temporary task names.
- Default features should stay minimal so optional capabilities are not compiled on every user's machine.
- Platform-specific, backend-specific, database-specific, and UI-specific capabilities should be optional features.
- After adding a feature, check whether it affects default compilation of other workspace crates.
- Avoid multiple features implicitly enabling the same heavy dependency set; when needed, document feature combinations clearly.

## AI Change Constraints

1. Modify one clear domain module at a time.
2. State affected files before editing.
3. Do not casually refactor unrelated modules.
4. Do not add `common.rs`, `helpers.rs`, `misc.rs`, or generic `utils.rs`.
5. Put new logic in a concrete domain directory first.
6. Public type, error type, config schema, or cross-module API changes must explain the impact.
7. New dependencies or features must explain the reason, alternatives, and compile-cost impact.
8. Rust code changes should run format and compile checks by default.

## Naming

- Types: prefer semantic names such as `RunConfig`, `TaskState`, `ProviderClient`.
- Errors: `XxxError`.
- Traits: describe capabilities, such as `ConfigStore`, `MessageTransport`.
- Pure logic functions: `calculate_xxx`, `resolve_xxx`, `evaluate_xxx`.
- I/O functions: verb plus object, such as `load_config`, `write_snapshot`, `fetch_catalog`.
- Test functions: describe the behavior result, not vague names.

## When To Split

Split when any of the following appears, or explain in the task output why you are not splitting yet:

- one file contains three or more of config, domain logic, I/O, entry scheduling, and test support
- one file exceeds 600 lines
- one function exceeds 80 lines
- a local change requires reading an entire giant file
- a new dependency serves one small feature but forces the whole workspace to compile many unrelated features

## Tests

Prioritize unit tests for pure logic. Cover I/O boundaries with temporary directories, mock transports, fixtures, or integration tests. Put cross-crate behavior tests in the relevant crate or workspace integration suite.

When adding a feature, run checks that cover at least the default feature set and the new feature where practical.

## Completion Checks

Rust code changes should run by default:

```bash
cargo fmt
cargo check --workspace
```

When test logic is involved, also run:

```bash
cargo test --workspace
```

When dependencies or features change, prefer targeted checks that cover the changed scope:

```bash
cargo tree -e features -p <crate>
cargo check -p <crate> --no-default-features --features <feature>
cargo check -p <crate>
```

Run `cargo check --workspace --all-features` only when the project requires it, the cost is acceptable, or full feature-combination verification is needed.

If checks cannot run, state the command, reason, and risk.
