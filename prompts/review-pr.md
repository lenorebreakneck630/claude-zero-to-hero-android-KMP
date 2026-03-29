<!--
  When to use:
  You want an AI-assisted code review of a pull request or a set of changed files.
  Paste the diff or individual file contents below and ask for a review against the
  project's skill patterns.

  Skills activated:
  android-presentation-mvi, android-data-layer, android-error-handling,
  android-coroutines-flow, android-testing, android-obfuscation-r8,
  android-security-encryption, android-di-koin, android-compose-ui

  Fill in every [PLACEHOLDER] before pasting.
-->

# Code review request

Review the following code against the project skill patterns. Be direct and specific.
For each finding, state the file + line range, the violated rule, and the recommended fix.

## PR / change context
- PR title / branch: `[PR_TITLE_OR_BRANCH]`
- Feature area: `[FEATURE_AREA]` (e.g. `Login`, `ArticleList`, `CheckoutFlow`)
- Modules changed: `[MODULES]`

## Code to review
```
[PASTE DIFF OR FILE CONTENTS HERE]
```

---

## Review checklist

### Architecture
- [ ] Follow **android-presentation-mvi**: is State immutable? Are side effects channelled
      through Events and not embedded in State? Is the Root/Screen composable split respected?
- [ ] Follow **android-data-layer**: does the repository return `Result<T, DataError>` and
      never throw across layer boundaries? Is the domain layer free of Android/framework imports?
- [ ] Follow **android-module-structure**: do module dependency arrows point in the correct
      direction? Does any module depend on `:app` or a sibling feature module directly?
- [ ] Follow **android-di-koin**: are all dependencies injected rather than constructed
      inline? Are Koin modules scoped correctly (single vs factory vs viewModel)?

### Error handling
- [ ] Follow **android-error-handling**: are all `Result` error types typed `DataError`
      variants? Is `runCatching` ever used without immediately mapping to a typed error?
- [ ] Are network errors distinguished from local storage errors?
- [ ] Are error messages mapped to `UiText` before reaching the UI layer?

### Coroutines and Flow
- [ ] Follow **android-coroutines-flow**: is `GlobalScope` used anywhere? Are coroutines
      launched in `viewModelScope` or a properly-scoped scope?
- [ ] Is `collect` called inside a composable without `collectAsStateWithLifecycle`?
- [ ] Are hot flows shared correctly (`shareIn` / `stateIn` with the right started policy)?
- [ ] Is `Dispatchers.Main` or `Dispatchers.IO` hardcoded in a ViewModel or repository
      instead of being injected?

### Compose UI
- [ ] Follow **android-compose-ui**: are lambdas passed to reusable composables stable
      (wrapped in `remember` or defined as `val`)? Are any unstable classes passed as
      composable parameters without `@Stable` or `@Immutable`?
- [ ] Are side effects (navigation, snackbar) handled in `LaunchedEffect` with the correct
      key, or via the event-channel pattern?

### Tests
- [ ] Follow **android-testing**: are new public ViewModel methods covered by unit tests?
      Are new repository methods covered? Are Turbine and AssertK used (not `assertEquals`)?
- [ ] Are fake dependencies used instead of mocks where the interface is simple?

### Obfuscation
- [ ] Follow **android-obfuscation-r8**: are new classes that cross serialization boundaries
      (DTOs, Parcelables, sealed classes used in navigation) annotated or have keep rules?
- [ ] Are any new Kotlin sealed interfaces or enums missing from the R8 keep rules?

### Security
- [ ] Follow **android-security-encryption**: are any secrets, tokens, or PII written to
      plain `SharedPreferences`, unencrypted files, or `Log` output?
- [ ] Is any sensitive data included in crash reports or analytics events without redaction?
- [ ] Are any new exported Activities, Services, or BroadcastReceivers declared without
      appropriate `android:exported` and permission checks?

---

## Output format
For each finding, use:
**[SEVERITY]** `[FILE]:[LINE_RANGE]` — [rule violated] — [recommended fix]

Severity levels: `BLOCKER` | `MAJOR` | `MINOR` | `NIT`

Finish with a short summary: overall assessment, blockers count, and whether the PR is
ready to merge once blockers are resolved.
