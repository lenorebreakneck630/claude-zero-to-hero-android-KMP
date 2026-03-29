<!--
  When to use:
  You have a ViewModel that is either untested or only partially tested. Use this prompt
  to generate a comprehensive unit test suite covering all state transitions, events,
  and error paths.

  Skills activated:
  android-testing, android-presentation-mvi, android-coroutines-flow

  Fill in every [PLACEHOLDER] before pasting.
-->

# Write ViewModel tests: [VIEWMODEL_NAME]

Follow **android-testing**, **android-presentation-mvi**, and **android-coroutines-flow** exactly.

## ViewModel under test
- ViewModel class: `[VIEWMODEL_NAME]` (e.g. `ArticleListViewModel`)
- State class: `[STATE_CLASS]` (e.g. `ArticleListState`)
- Action sealed interface: `[ACTION_CLASS]` (e.g. `ArticleListAction`)
- Event sealed interface (if any): `[EVENT_CLASS]` (e.g. `ArticleListEvent`)
- Injected dependencies: `[DEPENDENCIES]` (e.g. `ArticleRepository`, `UserPreferences`)
- Module / file location: `[FILE_PATH]`

## Paste the ViewModel source below
```kotlin
[PASTE VIEWMODEL SOURCE HERE]
```

## Tasks

### 1. Test class setup
- Annotate with `@ExtendWith(CoroutinesTestExtension::class)` (or set up
  `UnconfinedTestDispatcher` manually via `Dispatchers.setMain` in `@BeforeEach`).
- Create a fake for every dependency listed in `[DEPENDENCIES]`:
  - `Fake[DEPENDENCY]` should implement the interface and expose mutable backing fields
    so individual tests can control what it returns.

### 2. Initial state test
- Assert that immediately after construction, `viewModel.state.value` equals the
  expected default `[STATE_CLASS]`.

### 3. State transition tests
For each meaningful state change (loading → success, loading → error, empty, etc.):
```
@Test
fun `[describe action or trigger] updates state to [expected state]`() = runTest {
    // Arrange: configure the fake dependency
    // Act: call viewModel.onAction([ACTION]) or trigger init
    // Assert: use Turbine viewModel.state.test { ... awaitItem() }
}
```
Cover every field in `[STATE_CLASS]` that can change.

### 4. Event tests
For each `[EVENT_CLASS]` variant:
```
@Test
fun `[describe trigger] emits [EventName]`() = runTest {
    viewModel.events.receiveAsFlow().test {
        viewModel.onAction([TRIGGERING_ACTION])
        assertThat(awaitItem()).isInstanceOf([EventName]::class.java)
        cancelAndIgnoreRemainingEvents()
    }
}
```

### 5. Error path tests
- For each `DataError` variant the ViewModel can receive, assert that the error is
  mapped to the correct `UiText` in `[STATE_CLASS].errorMessage` (or equivalent field).
- Assert no uncaught exceptions escape the ViewModel.

### 6. Cancellation / repeated action tests
- If the ViewModel cancels an in-flight job on a new action (e.g. search debounce),
  write a test that dispatches two actions quickly and asserts only the second result
  is emitted.

### 7. SavedStateHandle tests (if applicable)
- If the ViewModel reads from `SavedStateHandle`, construct it with a pre-populated
  `SavedStateHandle(mapOf("key" to value))` and assert the correct initial state.

## Constraints
- Use `assertThat(actual).isEqualTo(expected)` from AssertK — not JUnit `assertEquals`.
- Use Turbine for all Flow assertions; do not use `first()` or `take(1).toList()`.
- Each test must be fully self-contained — no shared mutable state between tests.
- All tests must pass without a real Android device or Robolectric.
- Test method names must follow: `given [context], when [action], then [outcome]`
  OR the backtick sentence style shown above — be consistent throughout the file.
