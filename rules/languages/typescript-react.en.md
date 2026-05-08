# TypeScript + React Rules

## Goal

These rules apply to TypeScript + React projects, including `.ts` logic files and `.tsx` component files. They can also serve as frontend boundary rules for AI-assisted development.

## Core Principles

1. Files must be small and clear; one file should do one thing.
2. UI and logic must be separated.
3. Hooks are logic units; React components are presentation units.
4. Prefer splitting over piling on.
5. Imperfection is acceptable; disorder is not.

Each change should answer: can AI complete the next change after reading only 3-5 files? If not, prioritize splitting boundaries.

## File Size

- Target: 300-400 lines.
- Warning line: 500 lines.
- Upper limit: 1000 lines.

Files above 500 lines must be evaluated for splitting; files above 1000 lines should not be delivered as complete by default. If splitting is not safe yet, explain why and provide a follow-up plan.

Allowed exceptions include Page/Screen containers, highly cohesive complex interactions, and short-term transition files, but the split direction must be marked or explained.

## Functions And Components

- Single-function target: under 60 lines.
- Single-function upper limit: 80 lines.
- Single-component target: UI plus logic under 300 lines.

When limits are exceeded, prefer splitting into `types.ts`, `constants.ts`, domain-local `utils.ts`, `components/*.tsx`, and `hooks/useXxx.ts`.

Do not create over-abstraction just to reduce line count; splits must clarify responsibilities.

## Hook Rules

Prefer extracting a Hook when any of the following appears:

- `useState`, `useEffect`, `useMemo`, `useRef`, or similar hooks.
- Async requests.
- Side effects such as subscriptions, timers, event listeners, DOM listeners.
- Repeated logic across components.
- More than 3 state values in one component.

Hook files should be named `useXxx.ts`, return data and methods rather than JSX, may compose other Hooks, must not cross domains, and should not own presentation structure or styling.

Do not disguise pure functions as Hooks. Calculation functions without React hooks or side effects should be named `calculateXxx`, `formatXxx`, `resolveXxx`, and similar.

## React Component Rules

`.tsx` component files should do only three things: call Hooks, organize UI, and bind events.

Component files should not own complex data processing, request logic, side-effect details, large utility functions, or heavy business decisions.

Event handlers may do light forwarding inside components; once they contain business decisions, async flow, or multi-step state changes, move them into a Hook.

## Directory Structure

Split by domain first, not by broad technology buckets.

Not recommended:

```text
utils/
common/
helpers/
services/
```

Recommended:

```text
featureA/
  Page.tsx
  components/
    Panel.tsx
    List.tsx
  hooks/
    useFeatureAState.ts
    useFeatureAActions.ts
  types.ts
  constants.ts
  utils.ts
```

`utils.ts` should contain only pure functions for the current domain and must not become a global junk drawer. Shared capabilities must have clear domain ownership.

## Hook Split Strategy

Evaluate splitting when a file shows these signals:

- more than 3 `useEffect` calls
- more than 5 `useState` calls
- logic clearly outweighs UI

Split directions include state management, event logic, side effects, request flow, form logic, modal state, and list pagination. Handle complex `useEffect` last; extract types, constants, pure functions, and local UI first.

## AI Coding Constraints

AI must not:

- use "overall optimization" to expand the scope
- casually refactor unrelated structures
- modify multiple domains in one pass
- change behavior without request
- edit unrelated files just to pass checks

AI must:

- handle one clear domain at a time
- state affected files before editing
- prefer splitting over piling on
- preserve behavior unless the task asks for behavior change
- run or report type/build checks

## Split Workflow

Recommended order:

1. Extract types, constants, and pure utilities.
2. Extract pure UI components.
3. Extract local Hooks such as forms, modals, lists.
4. Extract page-level Hooks.
5. Handle complex `useEffect` last.

Each step should preserve behavior and keep the diff reviewable.

## Common Interaction Patterns

Prefer extracting Hooks for repeated patterns such as:

- loading plus request plus error
- form submit
- pagination
- modal state
- local filtering and sorting
- keyboard shortcuts
- drag state
- polling or subscription

## Verification

Frontend code changes should run the project's type check, build, or test command. For visual interaction changes, prefer browser verification.
