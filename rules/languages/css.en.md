# CSS Rules

## Goal

These rules apply to hand-written CSS. The goal is to keep style files removable, portable, and reviewable, and to prevent a single global CSS file from growing without boundaries.

## File Size

- Hand-written CSS files must stay under 1000 lines.
- Files above 900 lines should be split before adding large new rule blocks.
- Aggregation entry files should contain only `@import`, not business styles.
- Generated files, third-party outputs, and build artifacts are outside this rule, but should not be committed unless the project explicitly requires them.

## File Boundaries

- Component-specific styles should live with the component or domain.
- Cross-page base styles belong in a shared styles directory.
- Global overrides for third-party UI libraries should be centralized in clearly named override files or split by domain.
- Static page styles may keep a central entry, but should be split by responsibilities such as base, layout, content, component, and responsive.
- Do not create meaningless splits just to reduce line count; split by layout, component, theme, override, or responsive responsibility.

## Theme Rules

- Use one file per theme; avoid mixing multiple themes in one large file.
- Theme index files should only aggregate with `@import`.
- Theme IDs, display names, defaults, and available theme lists should have a single source of truth.
- The default theme CSS must provide base variables available at application startup.
- When adding, deleting, or renaming a theme, update theme CSS, theme index, and theme metadata together.
- When deleting a theme, confirm there are no stale theme ID references in code.

## Import Order

- CSS splits must preserve the original cascade order.
- When changing an aggregation entry, compare expanded content to confirm no rules were lost or reordered.
- CSS `@import` paths must be relative to the current CSS file, not the TS/TSX caller.

## Verification

After changing CSS, run at least:

```bash
rg --files -g '*.css' | while read -r f; do n=$(wc -l "$f" | awk '{print $1}'); if [ "$n" -gt 1000 ]; then echo "$n $f"; fi; done
```

For frontend style or theme changes, also run the project's build, type check, or style validation command, for example:

```bash
npm run build
```
