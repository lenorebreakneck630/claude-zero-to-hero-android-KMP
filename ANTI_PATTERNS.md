# Android Anti-Patterns

A layer-by-layer reference of what NOT to do. Each entry names the pattern, shows what it looks like, explains why it's wrong, and gives the correct alternative.

Use this document as a prompt prefix when doing code review:

> "Review this code against ANTI_PATTERNS.md and the relevant skill files. Flag any violations."

---

## Presentation Layer

### AP-01: Business Logic in Composables

**What it looks like:**
```kotlin
@Composable
fun NoteListScreen(repository: NoteRepository) {
    val notes = remember { mutableStateOf(emptyList<Note>()) }
    LaunchedEffect(Unit) {
        notes.value = repository.getNotes().getOrDefault(emptyList())
    }
    // ...
}
```

**Why it's wrong:** Composables cannot be unit-tested without a device. Logic in composables cannot be reused across surfaces. Process death loses all state.

**Instead:** All logic lives in a `ViewModel`. The composable only renders state and dispatches actions. See `android-presentation-mvi`.

---

### AP-02: Collecting Flow Without Lifecycle Awareness

**What it looks like:**
```kotlin
LaunchedEffect(Unit) {
    viewModel.state.collect { state ->
        // renders state
    }
}
```

**Why it's wrong:** `LaunchedEffect` with `Unit` keeps collecting even when the app is in the background, wasting resources and potentially causing UI updates when there is no visible surface.

**Instead:**
```kotlin
val state by viewModel.state.collectAsStateWithLifecycle()
```
`collectAsStateWithLifecycle()` stops collection when the lifecycle drops below `STARTED`.

---

### AP-03: `mutableStateOf` Inside ViewModel

**What it looks like:**
```kotlin
class NoteViewModel : ViewModel() {
    var notes by mutableStateOf(emptyList<Note>())
        private set
}
```

**Why it's wrong:** `mutableStateOf` is a Compose runtime concept. Using it in a ViewModel couples the ViewModel to Compose and makes it untestable without Compose infrastructure.

**Instead:** Use `MutableStateFlow` + `StateFlow`. See `android-presentation-mvi`.

---

### AP-04: Calling Suspend Functions Directly from Composables

**What it looks like:**
```kotlin
@Composable
fun SaveButton(repository: NoteRepository) {
    Button(onClick = {
        // ERROR: suspend function called from non-coroutine context
        repository.saveNote(note)
    }) { Text("Save") }
}
```

**Why it's wrong:** Click handlers are not coroutine contexts. This won't compile — and even workarounds (like `runBlocking`) block the main thread.

**Instead:** Dispatch an `Action` to the ViewModel and let it launch the coroutine.

---

### AP-05: Navigation Logic in Composables

**What it looks like:**
```kotlin
@Composable
fun LoginScreen(navController: NavController, viewModel: LoginViewModel) {
    val state by viewModel.state.collectAsStateWithLifecycle()
    if (state.isLoggedIn) {
        navController.navigate(Routes.Home)
    }
}
```

**Why it's wrong:** Navigation triggered from recomposition fires multiple times. `if (state.isLoggedIn)` re-evaluates on every recompose.

**Instead:** Emit a one-shot `Event` from the ViewModel and handle it with `ObserveAsEvents`. See `android-presentation-mvi`.

---

### AP-06: Passing ViewModel Instances Down the Composable Tree

**What it looks like:**
```kotlin
@Composable
fun ParentScreen(viewModel: ParentViewModel) {
    ChildComponent(viewModel = viewModel)
}
```

**Why it's wrong:** Tight coupling, breaks composable reuse, makes preview impossible.

**Instead:** Pass only the state and callbacks the child needs.

---

## Data Layer

### AP-07: Returning Room Entities from DAOs Directly to ViewModels

**What it looks like:**
```kotlin
// ViewModel
val notes: StateFlow<List<NoteEntity>> = dao.getAllNotes().stateIn(...)
```

**Why it's wrong:** Room entities are a persistence detail. ViewModels depending on them means a schema change breaks the entire presentation layer.

**Instead:** Map entities to domain models in the data source. The ViewModel only sees domain models or UI models. See `android-data-layer`.

---

### AP-08: Exposing Room Entities Outside the Data Module

**What it looks like:**
```kotlin
// :feature module depending on :data
class NoteViewModel(private val dao: NoteDao) // dao is a Room type
```

**Why it's wrong:** Room is an implementation detail of the data module. Other modules should depend on interfaces, not Room classes.

**Instead:** Define a `NoteRepository` interface in the domain layer. The `:data` module implements it. The `:feature` module depends on the interface only.

---

### AP-09: `runBlocking` in Repositories or Data Sources

**What it looks like:**
```kotlin
fun getNote(id: String): Note = runBlocking {
    dao.getNote(id)
}
```

**Why it's wrong:** `runBlocking` blocks the calling thread. Called from the main thread → ANR. It also defeats the purpose of coroutines.

**Instead:** Make the function `suspend` and let the caller manage the coroutine context.

---

### AP-10: Missing Domain Interface — ViewModel Depends on Concrete Data Class

**What it looks like:**
```kotlin
class NoteViewModel(
    private val repository: RoomNoteRepository // concrete class
)
```

**Why it's wrong:** Cannot be tested without a real Room database. Changes to implementation leak into the presentation layer.

**Instead:**
```kotlin
class NoteViewModel(
    private val repository: NoteRepository // interface in domain layer
)
```
Inject `RoomNoteRepository` via Koin. Use `FakeNoteRepository` in tests.

---

### AP-11: Swallowing Errors with Empty Catch Blocks

**What it looks like:**
```kotlin
try {
    val result = api.fetchNotes()
    emit(result)
} catch (e: Exception) {
    // nothing
}
```

**Why it's wrong:** Failures are silently ignored. The UI shows stale data or nothing, with no explanation.

**Instead:** Map exceptions to typed `DataError` and emit as `Result.Error`. See `android-error-handling`.

---

## Dependency Injection

### AP-12: Injecting `Context` into a ViewModel

**What it looks like:**
```kotlin
class NoteViewModel(private val context: Context) : ViewModel()
```

**Why it's wrong:** Holding an Activity context leaks the Activity. Holding Application context still couples the ViewModel to Android — untestable in JVM unit tests.

**Instead:** Extract the logic that needs `Context` into a data source or helper class in the data layer. Inject that abstraction into the ViewModel.

---

### AP-13: Creating Dependencies Manually Inside a Class

**What it looks like:**
```kotlin
class NoteViewModel : ViewModel() {
    private val repository = RoomNoteRepository(AppDatabase.getInstance())
}
```

**Why it's wrong:** Cannot swap implementations for testing. Violates dependency inversion.

**Instead:** Inject all dependencies via the constructor. Use Koin (or another DI framework) to wire them. See `android-di-koin`.

---

### AP-14: Putting Everything in One Koin Module

**What it looks like:**
```kotlin
val appModule = module {
    // 50+ bindings for every layer
}
```

**Why it's wrong:** Unreadable, impossible to scope correctly, defeats modular architecture.

**Instead:** One module per layer/feature. Assemble them in `:app`. See `android-di-koin`.

---

## Coroutines and Flow

### AP-15: Using `GlobalScope`

**What it looks like:**
```kotlin
GlobalScope.launch {
    repository.sync()
}
```

**Why it's wrong:** `GlobalScope` coroutines are never cancelled. They survive Activity and ViewModel destruction. Leaks resources, can produce unexpected side effects.

**Instead:** Use `viewModelScope` in ViewModels, `lifecycleScope` in Activities/Fragments, or inject a scoped `CoroutineScope` for longer-lived work. Use WorkManager for deferrable background work.

---

### AP-16: Not Cancelling Flow Collection

**What it looks like:**
```kotlin
// In a Service or non-lifecycle component:
repository.notes.collect { /* process */ }
// No cancellation — runs forever
```

**Why it's wrong:** Collection never stops, even when the component is destroyed.

**Instead:** Collect in a scoped coroutine (`lifecycleScope`, `viewModelScope`, or a `CoroutineScope` that is cancelled on destroy).

---

### AP-17: Thread.sleep in Coroutines

**What it looks like:**
```kotlin
viewModelScope.launch {
    Thread.sleep(1000) // blocking sleep in a coroutine
    loadData()
}
```

**Why it's wrong:** `Thread.sleep` blocks the thread, not just the coroutine. On `Dispatchers.Main` this causes jank. On a limited thread pool this starves other coroutines.

**Instead:** Use `delay(1000)` — it suspends the coroutine without blocking the thread.

---

### AP-18: Calling `.value` on StateFlow Inside a Suspend Function to "Wait"

**What it looks like:**
```kotlin
suspend fun waitForLogin() {
    while (!authState.value.isLoggedIn) {
        delay(100) // polling
    }
}
```

**Why it's wrong:** Polling wastes CPU and has race conditions. Misses emissions that happen between polls.

**Instead:** Use `first { it.isLoggedIn }` to suspend until the condition is met:
```kotlin
suspend fun waitForLogin() {
    authState.first { it.isLoggedIn }
}
```

---

### AP-19: Creating a New Flow Instance Per Subscriber

**What it looks like:**
```kotlin
val notes: Flow<List<Note>> get() = dao.getAllNotes() // re-evaluated each access
```

**Why it's wrong:** Each subscriber opens a new database cursor. Multiple collectors cause redundant queries.

**Instead:** Use `stateIn` or `shareIn` to share a single upstream subscription:
```kotlin
val notes: StateFlow<List<Note>> = dao.getAllNotes()
    .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), emptyList())
```

---

## Testing

### AP-20: Mocking Everything Instead of Using Fakes

**What it looks like:**
```kotlin
val repository = mockk<NoteRepository>()
every { repository.getNotes() } returns flowOf(emptyList())
```

**Why it's wrong:** Mocks test implementation details (which methods were called), not behaviour. They break on internal refactors. They couple tests to implementation.

**Instead:** Write a `FakeNoteRepository` that implements the interface with in-memory state. Tests are more readable and resilient. See `android-testing`.

---

### AP-21: Testing Implementation Details

**What it looks like:**
```kotlin
verify { viewModel.loadNotes() } // verifying a private function was called
```

**Why it's wrong:** Internal implementation changes break the test even when behaviour is correct.

**Instead:** Test observable outputs — state changes, emitted events, values in the repository.

---

### AP-22: `Thread.sleep` in Tests

**What it looks like:**
```kotlin
viewModel.loadNotes()
Thread.sleep(500) // wait for coroutine to finish
assertEquals(...)
```

**Why it's wrong:** Flaky — timing varies across machines. Slow — even if 500ms is enough, you're always paying 500ms.

**Instead:** Use `runTest` with `advanceUntilIdle()` or collect with Turbine. See `android-testing`.

---

### AP-23: Not Testing the Error Path

**What it looks like:**
Tests only cover the happy path. `Result.Error` cases are never exercised.

**Why it's wrong:** Error handling bugs are the most common production issues. Untested error paths break silently.

**Instead:** Write explicit tests for each error state. Use `FakeRepository` to inject errors.

---

## Build and Release

### AP-24: Committing Keystore Passwords in Source Control

**What it looks like:**
```kotlin
// build.gradle.kts
signingConfig {
    storePassword = "mypassword123"
    keyPassword = "mypassword123"
}
```

**Why it's wrong:** Anyone with repo access can sign APKs as your app. Leaked signing keys cannot be revoked without losing the Play Store listing.

**Instead:** Read passwords from environment variables or `local.properties` (gitignored). See `android-ci-cd-release`.

---

### AP-25: `debuggable = true` in Release Builds

**What it looks like:**
```kotlin
buildTypes {
    release {
        isDebuggable = true
    }
}
```

**Why it's wrong:** Allows attaching a debugger to production builds. Enables reading memory and bypassing security controls.

**Instead:** `isDebuggable` must be `false` in release. Only `debug` buildType should have it enabled.

---

### AP-26: Not Storing R8 Mapping Files Per Release

**What it looks like:**
Shipping a release without archiving `mapping.txt`. A production crash arrives with an obfuscated stack trace and no way to deobfuscate it.

**Why it's wrong:** Obfuscated crash traces are unreadable. Without the mapping file, the crash cannot be diagnosed.

**Instead:** Archive `mapping.txt` for every release build (CI artifact, Firebase Crashlytics auto-upload, or both). See `android-obfuscation-r8`.

---

### AP-27: Hardcoded API Keys in Source Code

**What it looks like:**
```kotlin
const val API_KEY = "sk-1234abcd..."
```

**Why it's wrong:** Keys are exposed in the APK (extractable with `apktool` even with R8). Anyone can use your quota or access your backend.

**Instead:** Keep secrets server-side. For keys that must be on-device, use `local.properties` + `BuildConfig` fields (not committed), and consider server-proxied calls for sensitive keys.

---

### AP-28: Shipping With `minifyEnabled = false` in Release

**What it looks like:**
```kotlin
buildTypes {
    release {
        isMinifyEnabled = false
    }
}
```

**Why it's wrong:** No code shrinking means larger APK, slower startup, and all class names readable in the binary.

**Instead:** Enable R8 with `isMinifyEnabled = true` and `isShrinkResources = true` in release. Test with a `releaseDebug` variant. See `android-obfuscation-r8`.

---

## Security

### AP-29: Storing Sensitive Data in SharedPreferences

**What it looks like:**
```kotlin
prefs.edit().putString("auth_token", token).apply()
```

**Why it's wrong:** `SharedPreferences` is plain XML on disk, readable by anyone with root access or a backup extraction.

**Instead:** Use `EncryptedSharedPreferences` or `DataStore` with encryption. For auth tokens, prefer the Android Keystore via `EncryptedSharedPreferences`. See `android-security-encryption`.

---

### AP-30: Trusting Client-Side Purchase Verification

**What it looks like:**
```kotlin
if (purchase.purchaseState == Purchase.PurchaseState.PURCHASED) {
    unlockFeature() // trusting client state only
}
```

**Why it's wrong:** Purchase state can be spoofed on rooted devices or by modifying memory.

**Instead:** Send the purchase token to your server, verify against the Google Play Developer API, and only unlock the feature after server confirmation. See `android-in-app-purchases`.

---

## Performance

### AP-31: Creating Objects Inside `onDraw` or Composable Lambdas

**What it looks like:**
```kotlin
Canvas(modifier) {
    val paint = Paint() // allocated on every frame
    drawCircle(...)
}
```

**Why it's wrong:** Object allocation inside draw calls triggers GC pressure and causes frame drops.

**Instead:** Hoist object creation outside the drawing lambda using `remember` in Compose or class-level fields in custom views.

---

### AP-32: Reading Files on the Main Thread

**What it looks like:**
```kotlin
// In Activity.onCreate()
val config = File(filesDir, "config.json").readText()
```

**Why it's wrong:** File I/O blocks the main thread. Slow storage → ANR.

**Instead:** Use `withContext(Dispatchers.IO)` to move file operations off the main thread.
