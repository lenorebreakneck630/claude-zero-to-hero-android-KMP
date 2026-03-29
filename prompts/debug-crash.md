<!--
  When to use:
  You have a crash report, ANR trace, or unexpected exception and you want AI assistance
  to identify the root cause, apply a fix following the project's skill patterns, and
  generate a regression test to prevent recurrence.

  Skills activated (auto-selected based on crash location):
  android-error-handling, android-coroutines-flow, android-presentation-mvi,
  android-data-layer, android-testing

  Fill in every [PLACEHOLDER] before pasting.
-->

# Debug crash / ANR: [SHORT_DESCRIPTION]

## Crash context
- App version: `[APP_VERSION]`
- Android API level(s) affected: `[API_LEVELS]` (e.g. `API 26+`, `all versions`)
- Frequency: `[FREQUENCY]` (e.g. `100% reproducible`, `~5% of sessions`)
- Triggered by: `[TRIGGER]` (e.g. `tapping the checkout button`, `opening the app cold`)
- Module / feature area: `[FEATURE_AREA]`

## Stack trace
```
[PASTE FULL STACK TRACE OR ANR TRACE HERE]
```

## Relevant source files
Paste any files mentioned in the stack trace that you have access to:

```kotlin
// [FILE_NAME_1]
[PASTE SOURCE HERE]
```

```kotlin
// [FILE_NAME_2 - add more blocks as needed]
[PASTE SOURCE HERE]
```

---

## What I need from you

### 1. Root cause analysis
- Identify the exact line and class where the crash originates.
- Explain *why* the crash happens — the underlying logic error, threading issue,
  null dereference, or lifecycle violation.
- Note any contributing conditions (e.g. only on process death, only when cache is empty).

### 2. Fix
Apply the fix following the relevant skills below. State which skill(s) apply:
- **android-error-handling** — if the crash is an unhandled exception crossing a layer boundary.
- **android-coroutines-flow** — if the crash involves a coroutine, Flow, or dispatcher issue
  (e.g. `IllegalStateException` from collecting on wrong dispatcher, cancelled scope).
- **android-presentation-mvi** — if the crash is in a ViewModel or UI state update
  (e.g. accessing a null state field, emitting after `onDestroy`).
- **android-data-layer** — if the crash is in a repository, data source, or mapper.
- **android-room-database** — if the crash involves a DAO, migration, or schema mismatch.
- **android-navigation** — if the crash involves a `NavController` or back stack issue.

Show the full corrected code for every changed function/class. Do not show a diff — show
the complete replacement.

### 3. Regression test
Write a unit test (or instrumented test if unavoidable) that:
- Reproduces the exact crash scenario.
- Passes after your fix is applied.
- Fails on the original broken code.

Follow **android-testing**: use JUnit5, AssertK, Turbine, `UnconfinedTestDispatcher`,
and fake dependencies. Name the test so it documents the bug
(e.g. `given empty cache, when repository is fetched, then no NullPointerException`).

### 4. Prevention recommendations
- List any broader patterns, lint rules, or code review checklist items that would
  have caught this bug before it reached production.
- If an R8 / obfuscation issue contributed, apply rules from **android-obfuscation-r8**.

## Constraints
- Do not silently swallow exceptions — map them to typed `DataError` variants.
- Do not use `!!` (non-null assertion) in the fix; use safe calls or explicit error handling.
- The fix must not change observable behaviour for the happy path.
