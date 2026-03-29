<!--
  When to use:
  You need to add a single new Compose screen with a ViewModel to an existing feature module.
  The screen follows the MVI pattern, has a navigation route, and is wired into Koin.

  Skills activated:
  android-presentation-mvi, android-compose-ui, android-navigation, android-di-koin, android-testing

  Fill in every [PLACEHOLDER] before pasting.
-->

# Add new screen: [SCREEN_NAME]

Follow **android-presentation-mvi**, **android-compose-ui**, **android-navigation**,
and **android-di-koin** exactly.

## Screen overview
- Screen name: `[SCREEN_NAME]` (e.g. `ProductDetail`, `OrderSummary`)
- Module it lives in: `:[MODULE_NAME]`
- Navigation route data passed in: `[INPUT_DATA]` (e.g. `productId: String`, or "none")
- Actions the user can perform: `[ACTIONS]` (e.g. `AddToCart`, `GoBack`, `Retry`)
- Events emitted (one-shot): `[EVENTS]` (e.g. `NavigateToCheckout`, `ShowSnackbar`)

## Tasks

### 1. MVI contracts
Create the following in `[SCREEN_NAME]ViewModel.kt`:
- `data class [SCREEN_NAME]State(...)` — all UI-visible data, defaults to a loading state.
- `sealed interface [SCREEN_NAME]Action` — one variant per user interaction listed above.
- `sealed interface [SCREEN_NAME]Event` — one variant per one-shot event listed above.

### 2. ViewModel
- Implement `[SCREEN_NAME]ViewModel` extending `ViewModel`.
- Expose `state: StateFlow<[SCREEN_NAME]State>` backed by `MutableStateFlow`.
- Expose `events: Channel<[SCREEN_NAME]Event>` consumed as a Flow.
- Handle each `[SCREEN_NAME]Action` in `fun onAction(action: [SCREEN_NAME]Action)`.
- Use `viewModelScope` + the correct dispatcher from **android-coroutines-flow**.
- Use `SavedStateHandle` to survive process death where relevant.

### 3. Composables
- Create `[SCREEN_NAME]Root` (stateful): collects `state`, observes `events`,
  calls `koinViewModel<[SCREEN_NAME]ViewModel>()`, delegates to `[SCREEN_NAME]Screen`.
- Create `[SCREEN_NAME]Screen` (stateless): accepts `state` and `onAction` lambda.
  No ViewModel reference inside this composable.
- Provide a `@Preview` for at least the default and error states.
- Follow all stability and recomposition rules from **android-compose-ui**.

### 4. Navigation route
- Define `@Serializable data class [SCREEN_NAME]Route([INPUT_DATA])` following
  **android-navigation**.
- Add a `fun NavGraphBuilder.[screenName]Screen(onNavigateUp: () -> Unit, ...)` extension.
- Register the extension in the feature's nav graph and wire up from `:app`.

### 5. Koin wiring
- Add `viewModel { [SCREEN_NAME]ViewModel(get()) }` to the feature's Koin module
  following **android-di-koin**.

### 6. Tests
- Write `[SCREEN_NAME]ViewModelTest` following **android-testing**:
  - Use `UnconfinedTestDispatcher` and `runTest`.
  - Assert each state transition with Turbine `awaitItem()`.
  - Assert each event with Turbine on the events flow.
  - Use a `Fake[DEPENDENCY]` for every injected dependency.

## Constraints
- `[SCREEN_NAME]Screen` must be pure — no side effects, no ViewModel calls.
- All user-facing strings go in `strings.xml`.
- Do not collect flows with `LaunchedEffect` directly on a ViewModel; use
  `ObserveAsEvents` or the event-channel pattern from **android-presentation-mvi**.
