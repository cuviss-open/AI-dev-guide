# Automation Scripting

## Language choice

- Default to Python for scripts that are maintained long-term, run cross-platform, or hold non-trivial logic (branching, config parsing, file operations, error handling).
- Bash is fine for a one-off, Unix-only operation of a few dozen lines.
- PowerShell is fine for a simple script that mainly drives Windows-only system capabilities.
- When bootstrapping a machine that lacks Python/uv, keep a thin `bootstrap.sh` / `bootstrap.ps1` that only prepares the runtime; the logic still lives in Python.
- Once a script reaches the complexity above or is expected to be maintained long-term, move it to Python.

## Existing-subsystem carve-out

- This standard governs new scripts and deliberate rewrites.
- A shell-based subsystem that already forms a system with an established contract (entry points such as `install` / `apply` / `check`) stays in its language; do not rewrite working code just to comply. A migration must be scoped as its own task, not done in passing.

## Python conventions

- Run and manage dependencies with uv; the entry form is `uv run path/to/script.py`.
- Add no third-party dependency without a clear need; prefer the standard library (`pathlib`, `subprocess`, `shutil`, `os`, `json`, `platform`, `tempfile`, …).
- When a single-file script genuinely needs one third-party dependency, declare it with PEP 723 inline metadata (`# /// script`) rather than adding a `requirements.txt` or similar.
- Use `pathlib` for file, directory, and path work; do not hand-join separators or assume a platform's path format.
- Prefer compatibility across macOS, Linux, WSL, and Windows; avoid needless platform-bound implementations.
- Detect platform differences with `platform`, `os.name`, `shutil.which()`; do not assume the running environment.

## External commands

- Call CLIs like `git`, `npm`, `cargo`, `docker` through a `subprocess` argument array; do not use `shell=True` by default.
  - Do: `subprocess.run(["git", "status"], check=True)`
  - Don't: `subprocess.run("git status", shell=True)`
- Check exit status explicitly; on failure, keep a clear error message and a non-zero exit code — never swallow it.

## Idempotency

- Make scripts safe to re-run and idempotent; work already done must not error or duplicate side effects on a second run.

## Structure

Split larger scripts by responsibility, moving platform detection, command execution, and config parsing into `lib/`, keeping entry files thin:

```
scripts/
├── check.py
├── update.py
└── lib/
    ├── platform.py
    ├── shell.py
    └── config.py
```

Name entry files after the project's verbs; do not fix rigid names (avoid `setup.py`, which carries setuptools meaning).
