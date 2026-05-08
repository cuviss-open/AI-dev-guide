# AGENTS.base.en.md

Host projects reference this file from the root `AGENTS.md` and add project-specific rules there. If rules conflict, the host project's root `AGENTS.md` wins.

## Language Rules

- Chinese files use `*.zh.md`; English files use `*.en.md`.
- AI should read only one language: prefer the host project's specified language; otherwise use the user's current conversation language.
- When modifying shared rules, README files, or public guidance in `AI-dev-guide/`, update the matching Chinese and English versions together.
- The root `README.md` is the bilingual entry exception; formal docs live in `docs/README.zh.md` and `docs/README.en.md`.

## Reading Order

1. The host project's root `AGENTS.md`.
2. `AI-dev-guide/AGENTS.base.en.md`.
3. `AI-dev-guide/rules/common/*.en.md`.
4. The host project's public entry document, usually `README.md`.
5. The smallest file set directly related to the task.
6. Other project-specific rules referenced by the host project.

Read these only when the task involves matching code or style changes:

- Rust: `AI-dev-guide/rules/languages/rust.en.md`
- TypeScript + React, including `.tsx` components: `AI-dev-guide/rules/languages/typescript-react.en.md`
- CSS: `AI-dev-guide/rules/languages/css.en.md`

## Git Authorization

- Commands such as `rm`, `git checkout -- <path>`, `git checkout .`, `git restore`, `git reset`, `git clean`, force push, history rewriting, and direct `.git/` edits are high-risk operations; do not run them unless the user explicitly names and allows the operation.
- Do not run `git commit` or `git push` without explicit user permission.
- Do not run highly destructive commands that delete, overwrite, rewrite, or move project files at scale without explicit user permission.
- Do not rewrite history, discard user changes, clean untracked files, force push, or reset the worktree without explicit user permission.
- After commit permission is granted, check the worktree before committing, ensure the diff contains only task-relevant files, and avoid committing secrets, caches, logs, local databases, dependency folders, and build output.

## Content Quality

- Documentation, code comments, and UI copy should contain only information that helps users act.
- Do not write self-explanatory content that explains why a file exists, restates platform basics, describes obvious structure, or narrates the current change.
- Avoid empty openings such as "this file is for", "this rule aims to", and "this section explains"; write the rule, entry point, command, or constraint directly.
- Explain reasons only when they affect usage, maintenance, security, or decisions.

## Completion Report

```text
Completed:
- ...

Changed files:
- ...

Verification:
- ...

Known risks:
- ...

Next:
- ...
```
