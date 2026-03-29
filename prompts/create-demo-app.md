<!--
  PROMPT: Create the TaskVault Demo App
  USE WHEN: You want to scaffold a complete Android demo app that exercises all 40 skills
            in the claude-zero-to-hero-android repo.
  HOW TO USE:
    1. Open a new empty Android project in Android Studio (Empty Activity, Kotlin, min SDK 26)
    2. Open Claude Code (or paste this into Claude Chat with the skill files attached)
    3. Paste this entire prompt
    4. Claude will scaffold the full app structure layer by layer
-->

# Build TaskVault — a complete Android demo app that exercises all 40 skills

## App concept

**TaskVault** is a personal productivity app where users create tasks, attach photos, pin tasks to their location, get reminders via push notifications, and sync everything offline-first. A premium tier unlocks advanced features. The app is multi-module, MVI, Compose-only, and demonstrates every skill in this repo working together.

## Skills to apply

Apply ALL of the following skills exactly as written. Do not deviate from their patterns:

**Architecture:**
`android-module-structure`, `android-presentation-mvi`, `android-compose-ui`, `android-navigation`,
`android-di-koin`, `android-build-logic-gradle`

**Data & async:**
`android-data-layer`, `android-error-handling`, `android-coroutines-flow`, `android-room-database`,
`android-datastore-preferences`, `android-workmanager`, `android-paging-offline-sync`,
`android-security-encryption`, `android-network-monitoring`

**Platform & capabilities:**
`android-auth-security`, `android-permissions-device-apis`, `android-deep-links`,
`android-notifications-push`, `android-media-playback-camera`, `android-app-startup-bootstrap`,
`android-file-storage-sharing`, `android-background-location-geofencing`,
`android-foldables-adaptive-ui`, `android-in-app-purchases`, `android-feature-flags`,
`android-text-input-forms`, `android-dynamic-feature-modules`, `android-image-loading-coil`,
`android-maps-location-ui`, `android-widgets-shortcuts`, `android-deep-links`,
`android-localization-accessibility`

**Quality & release:**
`android-testing`, `android-screenshot-testing`, `android-instrumentation-testing`,
`android-analytics-logging`, `android-crash-reporting`, `android-performance-profiling`,
`android-obfuscation-r8`, `android-ci-cd-release`

---

## Module structure

Follow `android-module-structure` exactly. Create these modules:

```
:app                          — app entry point, DI assembly, nav host
:core:domain                  — domain models, repository interfaces, use cases
:core:data                    — repository implementations, DTOs, mappers, Room, Ktor
:core:ui                      — shared Compose components, theme, UiText, typography
:core:common                  — Result, DataError, extension functions
:feature:auth                 — login, register, session state
:feature:tasklist             — paged task list, offline-first
:feature:taskdetail           — task detail, photo attachment, location tag
:feature:settings             — user preferences, theme, account
:feature:premium              — in-app purchases, entitlement gating
:feature:camera               — dynamic feature module (on-demand delivery)
```

Use convention plugins (`android-build-logic-gradle`) for shared Gradle config. Use a version catalog (`libs.versions.toml`).

---

## Screens and features

### 1. Splash / Bootstrap
- App Startup `Initializer` for: Koin, Firebase, Crashlytics, analytics
- SplashScreen API with animated icon
- Redirect to Login if no session, else TaskList
- Baseline Profile generation setup
- **Skills:** `android-app-startup-bootstrap`, `android-auth-security`, `android-navigation`

### 2. Login / Register screen
- Email + password form with validation (submit-time, then server-side errors via UiText)
- Password field with show/hide toggle
- Session persisted in EncryptedSharedPreferences
- Token refresh logic in AuthRepository
- Deep link: `taskvault://login?redirect=[destination]`
- **Skills:** `android-auth-security`, `android-text-input-forms`, `android-security-encryption`, `android-deep-links`, `android-presentation-mvi`

### 3. Task list screen (main screen)
- Paged list from Room + remote API (RemoteMediator)
- Offline banner when connectivity is lost (NetworkMonitor)
- FAB → Create Task
- Swipe to complete / delete
- Pull-to-refresh
- Adaptive layout: single column on phone, master-detail on tablet/foldable
- **Skills:** `android-paging-offline-sync`, `android-room-database`, `android-network-monitoring`, `android-foldables-adaptive-ui`, `android-presentation-mvi`, `android-compose-ui`

### 4. Create / Edit task form
- Fields: title, description, due date, priority, location tag, photo attachment
- Validate on submit; show field-level errors
- Photo: CameraX capture or gallery pick (REQUEST_PERMISSION → capture → save to MediaStore)
- Location tag: current location via FusedLocationProviderClient
- **Skills:** `android-text-input-forms`, `android-media-playback-camera`, `android-permissions-device-apis`, `android-file-storage-sharing`, `android-background-location-geofencing`

### 5. Task detail screen
- Shows full task with photo (Coil async image)
- Map snippet showing task location (Google Maps or MapBox)
- Share task via Intent.ACTION_SEND (FileProvider for photo)
- Delete → confirmation dialog
- Deep link: `taskvault://task/[id]`
- **Skills:** `android-image-loading-coil`, `android-maps-location-ui`, `android-file-storage-sharing`, `android-deep-links`

### 6. Notifications
- FCM push: receive task reminder, tap → navigate to task detail
- Local notification scheduled via WorkManager (due-date reminders)
- Notification channels: `reminders`, `sync_status`
- POST_NOTIFICATIONS permission request on Android 13+
- **Skills:** `android-notifications-push`, `android-workmanager`, `android-permissions-device-apis`

### 7. Background sync
- WorkManager periodic sync every 15 minutes when on WiFi
- Geofence: notify user when they arrive at a task's pinned location
- Re-register geofences on BOOT_COMPLETED
- **Skills:** `android-workmanager`, `android-background-location-geofencing`, `android-data-layer`

### 8. Settings screen
- Theme (light/dark/system) — DataStore backed
- Notification preferences — DataStore backed
- Sync frequency — DataStore backed
- Sign out
- Export all tasks as JSON to Downloads (DownloadManager / SAF)
- **Skills:** `android-datastore-preferences`, `android-file-storage-sharing`, `android-auth-security`, `android-presentation-mvi`

### 9. Premium screen
- Show feature list (locked vs unlocked)
- Play Billing: subscribe or one-time purchase
- Server-side verification stub (interface + fake)
- Entitlement state in a repository as `Flow<Set<Entitlement>>`
- Feature-flag gate: `PREMIUM_SCREEN_V2` flag controls new layout
- **Skills:** `android-in-app-purchases`, `android-feature-flags`, `android-presentation-mvi`

### 10. Camera dynamic feature
- Lives in `:feature:camera` as an on-demand dynamic feature module
- SplitInstallManager install flow with progress + error states
- Launched from task creation when camera module not yet installed
- **Skills:** `android-dynamic-feature-modules`

### 11. Widget & shortcuts
- Home screen widget: shows count of tasks due today, tap → opens task list
- Pinned shortcut: "New Task" → opens create form directly
- **Skills:** `android-widgets-shortcuts`

---

## Domain models

```kotlin
data class Task(
    val id: String,
    val title: String,
    val description: String,
    val dueAt: Instant?,
    val priority: Priority,
    val isCompleted: Boolean,
    val photoUri: String?,
    val latitude: Double?,
    val longitude: Double?,
    val createdAt: Instant,
)

enum class Priority { LOW, MEDIUM, HIGH }

data class UserSession(
    val userId: String,
    val accessToken: String,
    val refreshToken: String,
)
```

---

## Error types

Follow `android-error-handling` exactly:

```kotlin
sealed interface DataError : Error {
    enum class Network : DataError { REQUEST_TIMEOUT, NO_INTERNET, SERVER_ERROR, UNAUTHORIZED, UNKNOWN }
    enum class Local : DataError { DISK_FULL, NOT_FOUND, UNKNOWN }
    enum class Auth : DataError { INVALID_CREDENTIALS, TOKEN_EXPIRED, ACCOUNT_LOCKED }
}
```

---

## DI modules (Koin)

Follow `android-di-koin`. One module per layer:
- `networkModule` — Ktor HttpClient
- `databaseModule` — Room database, DAOs
- `dataModule` — repository implementations, data sources
- `domainModule` — use cases (if needed)
- `platformModule` — NetworkMonitor, CrashReporter, AnalyticsTracker, FeatureFlagRepository
- One `featureModule` per `:feature:*`
- Assembled in `:app`

---

## Analytics events

Follow `android-analytics-logging`:

```kotlin
sealed interface AnalyticsEvent {
    data class ScreenView(val screenName: String) : AnalyticsEvent
    data class TaskCreated(val priority: String) : AnalyticsEvent
    data class TaskCompleted(val taskId: String) : AnalyticsEvent
    data class PurchaseStarted(val productId: String) : AnalyticsEvent
    data class FeatureFlagExposure(val flagName: String, val value: Boolean) : AnalyticsEvent
}
```

---

## Feature flags

Follow `android-feature-flags`:

```kotlin
sealed interface FeatureFlag {
    data object PremiumScreenV2 : FeatureFlag   // new premium UI layout
    data object MapTaskPin : FeatureFlag         // location tagging on tasks
    data object GeofenceReminders : FeatureFlag  // geofence-based notifications
}
```

---

## Testing requirements

For every ViewModel, write unit tests following `android-testing`:
- Test all state transitions (loading → success, loading → error)
- Test all emitted events (navigation, snackbar)
- Use `FakeRepository` implementations — never mock
- Use `UnconfinedTestDispatcher` and `runTest`
- Use Turbine for Flow assertions

For key screens write screenshot tests following `android-screenshot-testing`:
- `TaskListScreen` — empty, loaded, offline states
- `LoginScreen` — default, error states
- Dark mode and RTL variants

---

## Adaptive layout rules

Follow `android-foldables-adaptive-ui`:
- `Compact` width → bottom navigation bar
- `Medium` width → navigation rail
- `Expanded` width → navigation drawer + task list/detail two-pane

---

## CI/CD

Follow `android-ci-cd-release`. Create `.github/workflows/`:
- `ci.yml` — on PR: lint, unit tests, screenshot verify, build debug
- `release.yml` — on tag: build release AAB, sign, upload to Play internal track
- Read keystore and passwords from GitHub Secrets
- Upload R8 mapping.txt as CI artifact

---

## R8 configuration

Follow `android-obfuscation-r8`. In `proguard-rules.pro`:
- Keep rules for: kotlinx.serialization, Ktor, Room, Koin, Firebase, Coil
- Store mapping file per release
- Enable `android.enableR8.fullMode=true` in `gradle.properties`

---

## Localization and accessibility

Follow `android-localization-accessibility`:
- All user-facing strings in `strings.xml` — no hardcoded strings in Compose
- `contentDescription` on all icon buttons and images
- Correct `semantics` on task list items (state: completed/pending)
- Test with TalkBack on the task list and create task screens

---

## Scaffold order

Build in this order so each layer is testable before the next:

1. Set up all Gradle modules and convention plugins (`:build-logic`, version catalog)
2. Define domain models, interfaces, and error types in `:core:domain` and `:core:common`
3. Implement Room database and DAOs in `:core:data`
4. Implement repositories (local + remote stubs) in `:core:data`
5. Set up Koin modules and wire everything in `:app`
6. Build `app-startup-bootstrap` (Initializer chain, SplashScreen)
7. Build `:feature:auth` (login screen, session management)
8. Build `:feature:tasklist` (paged list, offline banner, adaptive layout)
9. Build `:feature:taskdetail` (photo, map, share)
10. Build `:feature:settings` (DataStore preferences)
11. Build `:feature:premium` (billing, entitlements, feature flags)
12. Add WorkManager sync + geofencing
13. Add FCM push notifications
14. Add widget and shortcuts
15. Add `:feature:camera` dynamic feature module
16. Write ViewModel unit tests for all features
17. Write screenshot tests for key screens
18. Set up CI/CD workflows
19. Configure R8 keep rules
20. Add analytics events and crash reporting boundaries

---

## Final checklist before considering the app complete

- [ ] All 40 skills have at least one concrete usage in the codebase
- [ ] Zero hardcoded strings — all in `strings.xml`
- [ ] Zero PII in crash reports or analytics events
- [ ] StrictMode enabled in debug — zero violations
- [ ] R8 mapping file generated on release build
- [ ] All ViewModels have unit tests with fakes (no mocks)
- [ ] Offline mode works: kill network, tasks still load from Room
- [ ] Adaptive layout renders correctly on Compact, Medium, and Expanded window sizes
- [ ] Push notification tap navigates to the correct task detail
- [ ] Dynamic camera feature installs on-demand and shows progress UI
