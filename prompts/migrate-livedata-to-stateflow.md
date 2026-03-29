<!--
  When to use:
  You have an existing ViewModel that uses LiveData / MutableLiveData and you want to
  migrate it to the MVI pattern using StateFlow, a State data class, Action sealed
  interface, and Event channel. Use this prompt to perform the migration safely.

  Skills activated:
  android-presentation-mvi, android-coroutines-flow, android-testing

  Fill in every [PLACEHOLDER] before pasting.
-->

# Migrate ViewModel from LiveData to StateFlow + MVI

Follow **android-presentation-mvi** and **android-coroutines-flow** exactly.

## ViewModel to migrate
- ViewModel class: `[VIEWMODEL_NAME]` (e.g. `DashboardViewModel`)
- Module / file path: `[FILE_PATH]`
- Current LiveData fields: `[LIVEDATA_FIELDS]`
  (e.g. `val articles: LiveData<List<Article>>`, `val isLoading: LiveData<Boolean>`)
- Current observer sites (fragments/activities): `[OBSERVER_SITES]`
  (e.g. `DashboardFragment`, `MainActivity`)

## Paste the existing ViewModel source below
```kotlin
[PASTE EXISTING VIEWMODEL SOURCE HERE]
```

---

## Migration tasks

### 1. Define MVI contracts
Create the following sealed types in the same file (or a companion contracts file):

```kotlin
data class [VIEWMODEL_NAME_PREFIX]State(
    // map every LiveData field to a property here
    // e.g. val articles: List<Article> = emptyList(),
    //      val isLoading: Boolean = false,
    //      val errorMessage: UiText? = null
    [STATE_FIELDS]
)

sealed interface [VIEWMODEL_NAME_PREFIX]Action {
    // one variant per user interaction that previously triggered a method call
    [ACTION_VARIANTS]
}

sealed interface [VIEWMODEL_NAME_PREFIX]Event {
    // one variant per one-shot navigation or UI command (Toast, Snackbar, Navigate)
    [EVENT_VARIANTS]
}
```

### 2. Replace LiveData with StateFlow
- Remove all `MutableLiveData` and `LiveData` fields.
- Add:
  ```kotlin
  private val _state = MutableStateFlow([VIEWMODEL_NAME_PREFIX]State())
  val state: StateFlow<[VIEWMODEL_NAME_PREFIX]State> = _state.asStateFlow()

  private val _events = Channel<[VIEWMODEL_NAME_PREFIX]Event>()
  val events = _events.receiveAsFlow()
  ```
- Replace every `_liveDataField.value = x` with a `_state.update { it.copy(...) }` call.
- Replace every `SingleLiveEvent` / navigation trigger with `_events.send(...)` inside
  a `viewModelScope.launch`.

### 3. Replace method calls with onAction
- Create `fun onAction(action: [VIEWMODEL_NAME_PREFIX]Action)`.
- Move the body of each existing public method into the corresponding `when` branch.
- Delete the old public methods (or keep as deprecated shims temporarily).

### 4. Update observers
For each site in `[OBSERVER_SITES]`:
- Remove `viewModel.[field].observe(viewLifecycleOwner) { ... }`.
- Replace with `collectAsStateWithLifecycle()` (Compose) or
  `lifecycleScope.launch { viewModel.state.collect { ... } }` (Fragment).
- Replace event observer with `ObserveAsEvents(viewModel.events) { ... }` (Compose) or
  `lifecycleScope.launch { viewModel.events.collect { ... } }` (Fragment).

### 5. Preserve all existing logic
- Do not change business logic, repository calls, or error handling during this migration.
- If a behaviour is unclear, add a `// TODO: confirm behaviour` comment and keep the
  original logic as-is.

### 6. Add tests after migration
Once the migration is complete, generate a full test suite for `[VIEWMODEL_NAME]`
following **android-testing**:
- Use `UnconfinedTestDispatcher` and `runTest`.
- Use Turbine to assert every state transition.
- Use Turbine to assert every event emission.
- Create `Fake[DEPENDENCY]` for each injected dependency.
- Cover: initial state, success path, each error variant, each Action variant.

## Constraints
- Do not introduce any new business logic during this migration.
- Do not use `observeForever` or `TestObserver` in the new tests — use Turbine only.
- All coroutine launches must use `viewModelScope`; no `GlobalScope`.
- The migrated ViewModel must compile and all existing tests must pass before adding
  new tests.
