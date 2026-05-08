# Context Management Rules

## Reading Strategy

- Read indexes, directories, and filenames before specific files.
- Search focused keywords before opening matching sections.
- Prefer line ranges over full large files.
- Within the same task, if a previously read file has unchanged modification time and size, reuse the prior content or summary instead of rereading the whole file.
- Do not copy long source, logs, or documents into the response.
- Do not repeat background that is unrelated to the task.

## Cache Checks

Before reusing previously read content, confirm:

- The file path is the same.
- The file modification time is unchanged; file size or Git diff status can be used as an additional signal.
- The decision does not depend on new output from a command just executed.
- The file is not a secret, cache, log, local database, or other content forbidden from context.

Reread the relevant small section when:

- The file was changed by the user, AI, a formatter, a generator, or a test command.
- The worktree status shows the file changed.
- The task needs exact line numbers, exact errors, or the latest config value.
- The previous read was only a summary and the current task needs specific code or rule text.

## Command Output Compression

Reduce long command output at the source first:

- Prefer built-in quiet, short, JSON, failure-summary, or structured output options.
- For test and build failures, keep failing files, assertions, line numbers, error codes, and the shortest useful stack trace before removing repeated progress, passing cases, and install noise.
- Local, deterministic, reviewable command-output compressors may be used; they must not become a required project runtime dependency.
- Compressors must preserve or allow access to raw output, especially failure logs, compiler errors, test assertion diffs, and migration output.
- Do not send secrets, real `.env` files, local caches, local databases, personal paths, or account data to cloud LLMs for summarization.
- LLM summaries are only suitable for second-pass analysis after an explicit human request; they must not be the default automatic filter or the only source of truth.

If compressed output is not enough to locate the issue, reread the relevant small section from raw output instead of expanding to the full log.

## Expanding Context

Read more files only when the current minimum context is not enough to decide.

Before expanding, confirm:

- The new file is directly relevant to the task.
- It will not read secrets, caches, logs, local databases, or other sensitive content.
- A narrower search or line range cannot replace full-file reading.

## Reporting Context

Final responses should report only task-relevant outcomes: changes, verification, risks, and next steps. Do not restate long rules, source, or background that you read.
