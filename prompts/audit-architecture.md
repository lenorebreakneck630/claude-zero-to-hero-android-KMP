<!--
  PROMPT: Architecture audit against all skills
  USE WHEN: You want a full review of an existing codebase — layer by layer —
            against the skill patterns in this repo.
  HOW TO USE:
    1. Open Claude Code in your Android project
    2. Paste this prompt
    3. Claude will read the codebase and report violations per layer
-->

# Architecture audit

## Goal

Review the entire codebase against the skill patterns in this repo. For each layer, identify violations, missing patterns, and concrete fixes. Output a prioritised list of issues with file locations.

## Skills to audit against

`android-module-structure`, `android-presentation-mvi`, `android-compose-ui`,
`android-data-layer`, `android-error-handling`, `android-coroutines-flow`,
`android-room-database`, `android-di-koin`, `android-navigation`, `android-testing`

Also check `ANTI_PATTERNS.md` for the full list of known mistakes.

## Audit checklist per layer

### Module structure
- [ ] Dependency direction: `feature` → `core:domain` only, never `core:data`
- [ ] No cross-feature imports
- [ ] Convention plugins used for all modules
- [ ] No `implementation(project(:feature:X))` in another feature module

### Presentation (MVI)
- [ ] Every screen has `State`, `Action`, `Event` — no raw lambdas as ViewModel API
- [ ] `ViewModel` exposes only `StateFlow<State>` + `onAction(Action)`
- [ ] Navigation events emitted via `Channel` / `SharedFlow`, not `StateFlow`
- [ ] Root composable collects state; Screen composable is stateless
- [ ] No business logic inside `@Composable` functions

### Compose UI
- [ ] No `ViewModel` instantiation inside composables (`hiltViewModel()` / `koinViewModel()` at root only)
- [ ] `LaunchedEffect` used for one-shot side effects, not `DisposableEffect`
- [ ] Stable types passed to composables — no raw `List`, `Map` without `@Immutable`
- [ ] All icon buttons have `contentDescription`

### Data layer
- [ ] Repositories return `Result<T, DataError>` — no raw exceptions crossing boundaries
- [ ] DTOs exist and are mapped to domain models — no domain models in data sources
- [ ] Remote data source contains only HTTP calls; local data source contains only DB calls

### Error handling
- [ ] `DataError` sealed interface covers `Network`, `Local`, `Auth`
- [ ] No `try/catch` in ViewModels — errors come through repository `Result`
- [ ] UI maps `DataError` to `UiText` via a mapper, never hard-codes strings

### Coroutines / Flow
- [ ] `viewModelScope` owns all coroutines in ViewModel
- [ ] `Dispatchers.IO` injected, not hardcoded (`coroutineScope.launch(Dispatchers.IO)`)
- [ ] `StateFlow` used for state; `Channel`/`SharedFlow` for events
- [ ] No `GlobalScope` usage anywhere

### DI (Koin)
- [ ] One Koin module per layer (`networkModule`, `databaseModule`, `dataModule`, `featureModule`)
- [ ] ViewModels registered with `viewModelOf`, not `single`
- [ ] No service locator calls (`get()`) outside module definitions

### Testing
- [ ] All ViewModels have unit tests
- [ ] Tests use `FakeRepository`, never `mockk` / `Mockito`
- [ ] `UnconfinedTestDispatcher` used in all ViewModel tests
- [ ] Turbine used for `StateFlow` / `Flow` assertions

## Output format

For each violation found:

```
[LAYER] path/to/File.kt:NN
Issue: <what is wrong>
Fix: <what to change, referencing the relevant skill>
Priority: HIGH | MEDIUM | LOW
```

Group by layer. List HIGH priority items first.
