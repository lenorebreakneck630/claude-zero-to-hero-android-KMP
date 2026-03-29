# Claude Zero to Hero — Android & KMP Skills

> **40 opinionated skill documents that turn Claude Code into a senior Android engineer on your team.**

[![Skills](https://img.shields.io/badge/skills-40-brightgreen?style=flat-square)](skills/)
[![Kotlin](https://img.shields.io/badge/kotlin-2.x-7F52FF?style=flat-square&logo=kotlin&logoColor=white)](https://kotlinlang.org)
[![Jetpack Compose](https://img.shields.io/badge/jetpack%20compose-✓-4285F4?style=flat-square&logo=android&logoColor=white)](https://developer.android.com/compose)
[![KMP](https://img.shields.io/badge/KMP-ready-orange?style=flat-square)](https://www.jetbrains.com/kotlin-multiplatform/)
[![Claude Code](https://img.shields.io/badge/claude%20code-compatible-blueviolet?style=flat-square)](https://claude.ai/code)
[![GitHub Copilot](https://img.shields.io/badge/github%20copilot-compatible-black?style=flat-square&logo=github)](https://github.com/features/copilot)

---

## What this repo is

A focused library of **skill documents** — one per Android/KMP topic — that give AI coding assistants (Claude Code, Claude.ai, GitHub Copilot) the context they need to produce production-quality, architecturally consistent code.

Each skill covers a single concern: patterns, typed code examples, implementation checklist, anti-pattern warnings, and pointers to related skills. Skills compose. Give Claude two or three together, and it produces code that is consistent across all layers.

**Live proof:** [TaskVault](https://github.com/Shekhar23/TaskVault) — a complete Android productivity app scaffolded from a single prompt using all 40 skills. Built with Claude Code.

---

## Repository layout

```text
skills/
  <skill-name>/
    SKILL.md              — pattern guide with Kotlin examples and checklist
prompts/
  scaffold-new-feature.md
  add-new-screen.md
  create-repository.md
  write-viewmodel-tests.md
  review-pr.md
  migrate-livedata-to-stateflow.md
  debug-crash.md
  create-demo-app.md      — scaffolds the full TaskVault demo (all 40 skills)
COMBINATIONS.md           — which skills to combine per feature type
ANTI_PATTERNS.md          — what NOT to do, layer by layer
```

---

## Get started in 60 seconds

```bash
# 1. Clone the repo
git clone https://github.com/<your-username>/claude-zero-to-hero-android
cd claude-zero-to-hero-android

# 2. Open your Android project in Claude Code
cd /path/to/your/android/project
claude

# 3. Ask Claude to scaffold a feature using skills from this repo
# Example: "Follow android-presentation-mvi and android-compose-ui.
#           Create a Notes list screen with empty, loading, and error states."
```

Or scaffold a **complete app from scratch** in one shot:

1. Create a new empty Android Studio project (Empty Activity, Kotlin, min SDK 26).
2. Open Claude Code inside it.
3. Paste the contents of [prompts/create-demo-app.md](prompts/create-demo-app.md).
4. Claude Code scaffolds all 40 skills into a working app.

---

## Skill catalog

### Core architecture (11 skills)

| Skill | What it covers |
|---|---|
| `android-module-structure` | Module layout, dependency direction, `core` vs `feature`, convention-plugin boundaries |
| `android-presentation-mvi` | Screen state, actions, events, `ViewModel` patterns, Root/Screen composable split |
| `android-compose-ui` | Compose stability, recomposition, side effects, previews, animations, accessibility basics |
| `android-navigation` | Type-safe Compose navigation, feature nav graphs, app-level wiring |
| `android-di-koin` | Koin module structure, bindings, `viewModelOf`, and app assembly |
| `android-testing` | ViewModel unit tests, Turbine, fakes (no mocks), dispatcher setup, Compose UI tests |
| `android-screenshot-testing` | Roborazzi golden images, CI diff checks, dark mode and RTL visual regression |
| `android-instrumentation-testing` | Espresso, UI Automator, Hilt test rules, end-to-end device tests |
| `android-build-logic-gradle` | Convention plugins, version catalogs, scalable shared build logic |
| `android-ci-cd-release` | CI checks, signed releases, versioning, rollout tracks, release automation |
| `android-obfuscation-r8` | R8 keep rules, shrinking, mapping files, library-specific ProGuard config |

### Data, async, and persistence (9 skills)

| Skill | What it covers |
|---|---|
| `android-error-handling` | Typed `Result`, `Error`, `DataError`, and shared failure-handling utilities |
| `android-data-layer` | Repositories, data sources, DTOs, mappers, Ktor, Room, offline-first patterns |
| `android-coroutines-flow` | `Flow`, `StateFlow`, dispatcher ownership, cancellation, `combine`, async orchestration |
| `android-room-database` | Room entities, DAOs, transactions, converters, migrations, local storage patterns |
| `android-datastore-preferences` | DataStore, settings models, preferences vs proto, migrations, corruption handling |
| `android-workmanager` | Background sync, unique work, constraints, retries, worker orchestration |
| `android-paging-offline-sync` | Paging 3, `RemoteMediator`, paged data flows, Room-backed large-list strategies |
| `android-security-encryption` | Keystore boundaries, encrypted local storage, biometrics, sensitive data protection |
| `android-network-monitoring` | ConnectivityManager, NetworkCallback, offline banner, network-aware retry flows |

### App capabilities and platform integration (20 skills)

| Skill | What it covers |
|---|---|
| `android-auth-security` | Login/logout, session state, token refresh, guarded navigation, secure auth flows |
| `android-permissions-device-apis` | Runtime permissions, activity result contracts, capability-based UI handling |
| `android-deep-links` | App links, inbound route validation, deferred navigation, auth-gated link handling |
| `android-image-loading-coil` | Coil image loading, placeholders, sizing, caching, image-heavy UI performance |
| `android-localization-accessibility` | String resources, plurals, RTL, semantics, screen readers, inclusive UI review |
| `android-performance-profiling` | Startup profiling, jank analysis, baseline profiles, measurement-first optimization |
| `android-analytics-logging` | Analytics events, structured logs, crash boundaries, redaction-safe telemetry |
| `android-crash-reporting` | Crashlytics setup, non-fatal exceptions, custom keys, ANR detection, crash boundaries |
| `android-widgets-shortcuts` | Widgets, launcher shortcuts, external entry points, app routing from launcher surfaces |
| `android-maps-location-ui` | Map screens, marker state, camera behavior, location-aware UI flows |
| `android-notifications-push` | FCM setup, notification channels, local notifications, deep-link on tap |
| `android-media-playback-camera` | Media3/ExoPlayer, CameraX, lifecycle integration, scoped storage media writes |
| `android-app-startup-bootstrap` | App Startup library, cold-start hygiene, SplashScreen API, Baseline Profiles |
| `android-file-storage-sharing` | Scoped storage, FileProvider, MediaStore, SAF, share sheets |
| `android-background-location-geofencing` | Foreground location service, geofencing API, battery-aware tracking |
| `android-foldables-adaptive-ui` | WindowSizeClass, two-pane layouts, NavigationSuiteScaffold, foldable posture |
| `android-in-app-purchases` | Play Billing Library, subscriptions, purchase verification, entitlement flows |
| `android-feature-flags` | Local toggles, Firebase Remote Config, flag-gated navigation, A/B testing basics |
| `android-text-input-forms` | TextField patterns, form state, validation, keyboard handling, focus traversal |
| `android-dynamic-feature-modules` | On-demand delivery, SplitInstallManager, dynamic navigation |

---

## Quick-reference: which skills for which task

| Task | Primary skills | Add if needed |
|---|---|---|
| New screen | `android-presentation-mvi` + `android-compose-ui` | `android-navigation`, `android-di-koin`, `android-testing` |
| API-backed feature | `android-data-layer` + `android-error-handling` | `android-coroutines-flow`, `android-testing` |
| Offline-first feature | `android-data-layer` + `android-room-database` | `android-paging-offline-sync`, `android-workmanager` |
| Login flow | `android-auth-security` + `android-presentation-mvi` | `android-di-koin`, `android-security-encryption` |
| User settings screen | `android-datastore-preferences` + `android-presentation-mvi` | `android-di-koin` |
| Background sync | `android-workmanager` + `android-data-layer` | `android-coroutines-flow`, `android-network-monitoring` |
| Paged list | `android-paging-offline-sync` + `android-room-database` | `android-data-layer`, `android-compose-ui` |
| Push notifications | `android-notifications-push` + `android-deep-links` | `android-permissions-device-apis` |
| Media / camera | `android-media-playback-camera` + `android-permissions-device-apis` | `android-file-storage-sharing` |
| Map + location | `android-maps-location-ui` + `android-background-location-geofencing` | `android-permissions-device-apis` |
| In-app purchases | `android-in-app-purchases` + `android-data-layer` | `android-analytics-logging` |
| Feature flag gating | `android-feature-flags` + `android-navigation` | `android-analytics-logging`, `android-datastore-preferences` |
| Adaptive / tablet layout | `android-foldables-adaptive-ui` + `android-compose-ui` | `android-navigation` |
| New module | `android-module-structure` + `android-build-logic-gradle` | `android-di-koin` |
| CI/CD setup | `android-ci-cd-release` + `android-build-logic-gradle` | `android-obfuscation-r8` |
| Performance audit | `android-performance-profiling` + `android-app-startup-bootstrap` | `android-analytics-logging` |
| Crash monitoring | `android-crash-reporting` + `android-analytics-logging` | `android-error-handling` |
| Full feature scaffold | `android-module-structure` + `android-presentation-mvi` + `android-compose-ui` + `android-data-layer` | `android-navigation`, `android-di-koin`, `android-testing`, `android-error-handling` |

For the full combination map with prompt hints, see [COMBINATIONS.md](COMBINATIONS.md).

---

## Prompt library

| Prompt | Use when |
|---|---|
| [scaffold-new-feature.md](prompts/scaffold-new-feature.md) | Building a complete feature across all layers |
| [add-new-screen.md](prompts/add-new-screen.md) | Adding a single Compose screen with MVI + navigation |
| [create-repository.md](prompts/create-repository.md) | Adding a new repository or data source |
| [write-viewmodel-tests.md](prompts/write-viewmodel-tests.md) | Generating unit tests for an existing ViewModel |
| [review-pr.md](prompts/review-pr.md) | Running a skill-aligned code review |
| [migrate-livedata-to-stateflow.md](prompts/migrate-livedata-to-stateflow.md) | Migrating LiveData to StateFlow + MVI |
| [debug-crash.md](prompts/debug-crash.md) | Diagnosing a crash or ANR with root-cause analysis |
| [create-demo-app.md](prompts/create-demo-app.md) | **Scaffolding the full TaskVault demo app (all 40 skills)** |

---

## How to use the skills

### With Claude Code (CLI / VS Code extension)

Claude Code has a built-in skill system. Skills from this repo activate automatically based on your task.

1. Clone or copy the `skills/` folder into your project (or keep it as a reference repo).
2. Install the skills into Claude Code — each `SKILL.md` maps to one named skill.
3. Describe what you want to build. Claude Code matches your request to the relevant skills and applies them.
4. Name skills explicitly in your prompt to narrow or override the automatic selection.

```
# Example prompt
"Create a new notes list screen with offline support."

# Claude Code automatically applies:
# android-presentation-mvi, android-compose-ui, android-navigation,
# android-data-layer, android-room-database, android-testing
```

**Recommended:** Add a `CLAUDE.md` file at your Android project root to lock in default skills:

```markdown
# CLAUDE.md
This is an Android/KMP project. Always follow these skills:
- android-presentation-mvi
- android-compose-ui
- android-data-layer
- android-error-handling
- android-di-koin
- android-testing
```

### With Claude.ai (web / desktop app)

1. Open the relevant `SKILL.md` files.
2. Attach them to the conversation or paste their content.
3. In your message, tell Claude to follow those skills strictly and name them explicitly.

```
"Follow android-data-layer, android-error-handling, and android-room-database exactly.
 Create an offline-first notes repository."
```

### With GitHub Copilot

Add a `.github/copilot-instructions.md` file to your Android project so Copilot Chat includes these rules automatically:

```markdown
Follow these Android patterns for all code in this project:
- MVI presentation layer: android-presentation-mvi
- Compose UI: android-compose-ui
- Data layer with typed errors: android-data-layer + android-error-handling
- Koin DI: android-di-koin
- Testing with fakes (no mocks): android-testing
```

For per-request overrides, reference skill files inline:

```
"Use #file:skills/android-data-layer/SKILL.md and
     #file:skills/android-room-database/SKILL.md
 to create an offline-first notes repository."
```

---

## Demo app — TaskVault

[prompts/create-demo-app.md](prompts/create-demo-app.md) is a single prompt that scaffolds **TaskVault**, a complete Android productivity app covering every skill in one real project.

**GitHub:** [Shekhar23/TaskVault](https://github.com/Shekhar23/TaskVault)

**What TaskVault covers:**

| Area | Skills used |
|---|---|
| Multi-module + convention plugins | `android-module-structure`, `android-build-logic-gradle` |
| App startup + splash + Baseline Profile | `android-app-startup-bootstrap`, `android-performance-profiling` |
| Login + session + encrypted storage | `android-auth-security`, `android-text-input-forms`, `android-security-encryption` |
| Paged offline-first task list | `android-paging-offline-sync`, `android-room-database`, `android-data-layer` |
| Offline banner | `android-network-monitoring` |
| Adaptive layout (phone / tablet / fold) | `android-foldables-adaptive-ui`, `android-compose-ui`, `android-navigation` |
| Create task form + photo + location | `android-media-playback-camera`, `android-permissions-device-apis`, `android-file-storage-sharing` |
| Task detail with map + share | `android-maps-location-ui`, `android-image-loading-coil`, `android-deep-links` |
| Push + local notifications | `android-notifications-push`, `android-workmanager` |
| Geofence reminders | `android-background-location-geofencing` |
| User settings | `android-datastore-preferences` |
| Premium / subscriptions | `android-in-app-purchases` |
| Feature flags + A/B | `android-feature-flags`, `android-analytics-logging` |
| Camera as on-demand module | `android-dynamic-feature-modules` |
| Widget + shortcuts | `android-widgets-shortcuts` |
| Crash reporting + ANR detection | `android-crash-reporting` |
| R8 + mapping file | `android-obfuscation-r8` |
| CI/CD | `android-ci-cd-release` |
| ViewModel unit tests + fakes | `android-testing` |
| Screenshot / visual regression | `android-screenshot-testing` |
| End-to-end device tests | `android-instrumentation-testing` |
| Strings + semantics + RTL | `android-localization-accessibility` |
| Typed Result + DataError | `android-error-handling`, `android-coroutines-flow` |
| Koin DI per layer | `android-di-koin` |

---

## Suggested usage pattern per task

```
1. Pick the primary skill for the main concern
2. Add supporting skills from adjacent layers (COMBINATIONS.md)
3. Use the matching prompt template from prompts/
4. Implement
5. Review against ANTI_PATTERNS.md using the same skills
```

Example for a full feature:

| Layer | Skill |
|---|---|
| Architecture | `android-module-structure` |
| State + UI | `android-presentation-mvi` + `android-compose-ui` |
| Data | `android-data-layer` + `android-error-handling` |
| Async | `android-coroutines-flow` |
| Tests | `android-testing` |

---

## Anti-patterns reference

[ANTI_PATTERNS.md](ANTI_PATTERNS.md) lists the most common mistakes layer by layer — use it as a final review checklist or pass it to Claude alongside the skill docs.

---

## KMP skills (new)

| Skill | What it covers |
|---|---|
| `android-kmp-shared-module` | `expect`/`actual`, source set split, what belongs in `commonMain` vs `androidMain` |
| `android-kmp-viewmodel` | Sharing ViewModel logic across Android and iOS, KMP-NativeCoroutines |
| `android-kmp-ktor-serialization` | Shared Ktor client + `kotlinx.serialization`, platform engine injection, `safeApiCall` |
| `android-kmp-sqldelight` | SQLDelight schema files, platform driver factory, Flow-backed queries |

## CLAUDE.md templates

Ready-to-copy config files in [templates/](templates/):

| File | Use for |
|---|---|
| [templates/CLAUDE.md.android](templates/CLAUDE.md.android) | Pure Android project (Compose + MVI + Koin) |
| [templates/CLAUDE.md.kmp](templates/CLAUDE.md.kmp) | Android + KMP shared module |

Copy the right template to your project root as `CLAUDE.md`.

## Glossary

[GLOSSARY.md](GLOSSARY.md) defines every shared term (`State`, `Action`, `Event`, `UiText`, `DataError`, `Fake`, etc.) used across the skill documents — include it when you want Claude to use your exact naming conventions.

---

## Roadmap — skills to add next

The following topics are natural extensions of this library:

| Skill | Why it's valuable |
|---|---|
| `android-kmp-shared-module` | `expect`/`actual`, commonMain/androidMain/iosMain split, what belongs in shared vs platform |
| `android-kmp-viewmodel` | Sharing ViewModels (or equivalents) across Android and iOS |
| `android-kmp-ktor-serialization` | Ktor + kotlinx.serialization for shared networking in KMP |
| `android-kmp-sqldelight` | SQLDelight as the KMP-native alternative to Room |
| `android-media-recording` | Audio recording, MediaRecorder, file output, permission flow |
| `android-accessibility-audit` | Automated a11y checks, TalkBack testing, content descriptions at scale |
| `android-app-shortcuts-dynamic` | Dynamic shortcut updates driven by app state |
| `android-wear-os` | Compose for Wear OS, health platform, tile and complication basics |
| `android-tv` | Lean-back UI, D-pad focus, TV Compose basics |

Contributions welcome — see the structure of any existing `SKILL.md` as a template.
