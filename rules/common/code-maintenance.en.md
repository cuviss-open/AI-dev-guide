# Code Maintenance Rules

## File Shape

- Prefer small files with clear responsibilities.
- Split by domain first, technology second.
- Avoid dumping unrelated behavior into `utils`, `helpers`, `common`, or `misc`.
- Keep root entry files thin as a project grows.

## Refactoring

Refactor only when it helps the current task or removes a real maintenance obstacle.

Good reasons:

- The next change would require reading a huge unrelated file.
- A behavior is duplicated in multiple places.
- A file mixes unrelated responsibilities.
- A risky side effect can be isolated behind a clearer boundary.

Bad reasons:

- The code is merely not your preferred style.
- A formatter would rewrite the entire file while the task is narrow.
- You want to rename everything before understanding local conventions.

## Verification

Run checks matched to the touched area. Prefer existing project scripts before inventing new ones.

If a full check is too expensive, run the narrowest useful check and state the tradeoff.
