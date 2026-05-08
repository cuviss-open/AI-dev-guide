# Repository Boundary Rules

## Recommended Boundaries

- Root `AGENTS.md`: collaboration rules and project-specific AI rules.
- Root `README.md`: public user and contributor entry point.
- Root scripts: stable commands users can run from the repository root.
- `src/`, `app/`, `packages/`, or project-defined folders: application code.
- `docs/`: user-facing documentation.
- `rules/common/`: shared coding and maintenance rules.
- `rules/languages/`: language-specific and style rules.
- Submodules: external repositories with their own history and boundaries.

## Submodules

Submodules should be treated as separate repositories.

- Do not edit a submodule unless the task explicitly targets it.
- When editing a submodule, check and report its own worktree status.
- After explicit user permission to commit is granted, commit submodule changes inside the submodule before updating the parent pointer.
- The parent repository should record the intended submodule commit.

## Generated And Dependency Folders

Do not hand-edit dependency or generated folders such as:

- `node_modules/`
- `dist/`, `build/`, `target/`, `.next/`, `.turbo/`
- generated SDKs or assets unless the project explicitly tracks them

Use the host project's generation command when updates are required.

## Highly Destructive Commands

The following commands can seriously damage a project and must be tightly restricted. Do not run them unless the user explicitly requests the operation and states the target:

- deleting or bulk-moving files: `rm`, `rm -r`, `rm -rf`, `mv`, bulk `find -delete`
- discarding or overwriting changes: `git checkout -- <path>`, `git restore`, `git reset --hard`
- cleaning untracked files: `git clean`, `git clean -fd`, `git clean -fdx`
- rewriting history or remote state: `git rebase`, `git commit --amend`, `git push --force`, `git push --force-with-lease`
- directly editing `.git/` or deleting submodule directories

After permission is granted, run a read-only check first, such as `git status --short`, `git diff --name-only`, or a target path listing, to confirm the impact before executing.
