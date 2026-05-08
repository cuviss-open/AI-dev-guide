# AI Dev Guide

AI Dev Guide is a reusable rule set for AI-assisted software development. It gives repositories a clear control layer: what AI should read first, what must stay out of context, how to keep changes reviewable, and how to verify work before finishing.

## Language File Convention

This repository uses filename suffixes to separate languages:

- `*.zh.md`: Chinese.
- `*.en.md`: English.

AI developers should read only one language: prefer the language specified by the host project's `AGENTS.md`; if none is specified, use the user's current conversation language. Read both languages only when translating, reviewing, or maintaining this guide.

## Add To A Project

```bash
git submodule add https://github.com/youyegit/AI-dev-guide.git AI-dev-guide
git submodule update --init --recursive
```

Then create or update the host repository's root `AGENTS.md`:

```md
# AGENTS.md

This project inherits `AI-dev-guide/AGENTS.base.en.md`.

AI developers must follow the shared guide first, then this project's local rules.

If rules conflict, this project's root `AGENTS.md` wins inside this repository.
```

## Repository Contents

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

Host projects should write their own root `AGENTS.md` and reference the shared baseline rule for the chosen language.

## Design Goals

- Keep AI changes small, reviewable, and reversible.
- Make the first-read path obvious for both humans and AI.
- Prevent secrets, tokens, real `.env` files, caches, logs, and local databases from entering AI context.
- Encourage domain-based modules instead of giant mixed-responsibility files.
- Make every development task end with concrete verification.

## Host Project Pattern

The host project should own project-specific truth: product goal, current stage, source layout, read-only directories, build and test commands, and domain rules.

This guide owns reusable process truth: AI collaboration behavior, context safety, general module boundaries, language rules, and completion reporting.

## License

Apache-2.0.
