# Security And Context Rules

## Forbidden Context

Do not read, print, summarize, commit, or include in prompts:

- API keys, tokens, passwords, private keys, certificates, and OAuth material.
- Real `.env` files or local override files containing secrets.
- Local databases, cache directories, generated logs, crash dumps, and telemetry exports.
- Browser profiles, cookie stores, shell history, and personal account data.
- Build outputs unless the task explicitly targets build artifacts.

## Safer Inspection Patterns

Prefer commands that output metadata only:

```bash
git status --short
git diff --name-only
rg --files
find . -maxdepth 2 -type f -name '*.md'
```

When checking for possible sensitive files, inspect filenames only, not contents:

```bash
rg --files -g '.env*' -g '*secret*' -g '*token*'
```

## If Sensitive Content Appears

1. Stop expanding that output.
2. Do not quote or summarize the sensitive value.
3. Report that sensitive content was encountered without repeating it.
4. Ask the human for a sanitized version if the task truly requires it.
