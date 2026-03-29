# Skill Combination Map

Quick lookup: given a feature type, which skills should you apply together?

## How to use
Pick your feature type below. Use all listed skills as context for the AI assistant.
Primary skill is listed first; supporting skills follow.

---

## Feature combinations

### New screen with state
**Skills:** `android-presentation-mvi` + `android-compose-ui` + `android-navigation`
**Add if needed:** `android-di-koin` (wire ViewModel), `android-testing` (test the ViewModel)
**Prompt hint:** "Create a [name] screen following android-presentation-mvi and android-compose-ui."

---

### New API-backed feature (fetch + display)
**Skills:** `android-data-layer` + `android-presentation-mvi` + `android-compose-ui` + `android-error-handling`
**Add if needed:** `android-di-koin` (wire repository and ViewModel), `android-coroutines-flow` (Flow pipelines), `android-testing` (fake repository tests)
**Prompt hint:** "Create a [name] feature that fetches from [endpoint] and displays the result, following android-data-layer, android-presentation-mvi, and android-error-handling."

---

### Offline-first feature (fetch, cache, display)
**Skills:** `android-data-layer` + `android-room-database` + `android-paging-offline-sync` + `android-presentation-mvi` + `android-error-handling`
**Add if needed:** `android-coroutines-flow` (Flow from Room), `android-di-koin` (wire all layers), `android-testing` (fake DAO / repository)
**Prompt hint:** "Create an offline-first [name] feature following android-data-layer, android-room-database, and android-paging-offline-sync."

---

### Login / authentication flow
**Skills:** `android-auth-security` + `android-presentation-mvi` + `android-compose-ui` + `android-navigation`
**Add if needed:** `android-security-encryption` (store tokens), `android-error-handling` (typed auth errors), `android-di-koin` (session module), `android-testing` (ViewModel and session tests)
**Prompt hint:** "Create a login flow following android-auth-security, android-presentation-mvi, and android-compose-ui."

---

### User settings screen
**Skills:** `android-datastore-preferences` + `android-presentation-mvi` + `android-compose-ui`
**Add if needed:** `android-di-koin` (inject preferences repository), `android-localization-accessibility` (labels and content descriptions), `android-testing` (test preference reads/writes)
**Prompt hint:** "Create a settings screen following android-datastore-preferences and android-presentation-mvi."

---

### Background sync job
**Skills:** `android-workmanager` + `android-data-layer` + `android-error-handling`
**Add if needed:** `android-di-koin` (inject worker dependencies), `android-coroutines-flow` (observe work state), `android-room-database` (persist sync results), `android-testing` (test the worker)
**Prompt hint:** "Create a background sync worker following android-workmanager and android-data-layer."

---

### Paged list (infinite scroll)
**Skills:** `android-paging-offline-sync` + `android-compose-ui` + `android-presentation-mvi`
**Add if needed:** `android-room-database` (RemoteMediator cache), `android-data-layer` (paged API source), `android-di-koin` (pager injection), `android-testing` (pager and ViewModel tests)
**Prompt hint:** "Create a paged list screen for [entity] following android-paging-offline-sync, android-compose-ui, and android-presentation-mvi."

---

### Push notifications
**Skills:** `android-notifications-push` + `android-permissions-device-apis` + `android-navigation`
**Add if needed:** `android-deep-links` (notification tap → destination), `android-analytics-logging` (track notification events), `android-di-koin` (inject notification handler)
**Prompt hint:** "Implement push notifications following android-notifications-push and android-permissions-device-apis."

---

### Media playback screen
**Skills:** `android-presentation-mvi` + `android-compose-ui` + `android-permissions-device-apis`
**Add if needed:** `android-coroutines-flow` (playback state as Flow), `android-di-koin` (inject player), `android-navigation` (playback route), `android-testing` (ViewModel tests)
**Prompt hint:** "Create a media playback screen following android-presentation-mvi and android-compose-ui."

---

### Camera / photo capture
**Skills:** `android-permissions-device-apis` + `android-compose-ui` + `android-presentation-mvi`
**Add if needed:** `android-image-loading-coil` (display captured images), `android-data-layer` (upload captured photo), `android-navigation` (camera route), `android-testing` (ViewModel tests)
**Prompt hint:** "Create a camera capture screen following android-permissions-device-apis, android-presentation-mvi, and android-compose-ui."

---

### Map screen with location
**Skills:** `android-maps-location-ui` + `android-permissions-device-apis` + `android-presentation-mvi` + `android-compose-ui`
**Add if needed:** `android-navigation` (map route), `android-data-layer` (fetch nearby items), `android-di-koin` (inject location source), `android-testing` (fake location ViewModel tests)
**Prompt hint:** "Create a map screen following android-maps-location-ui, android-permissions-device-apis, and android-presentation-mvi."

---

### In-app purchases / subscriptions
**Skills:** `android-presentation-mvi` + `android-compose-ui` + `android-error-handling`
**Add if needed:** `android-auth-security` (gate purchases behind session), `android-di-koin` (billing client injection), `android-analytics-logging` (purchase events), `android-testing` (billing ViewModel tests)
**Prompt hint:** "Create an in-app purchase flow following android-presentation-mvi, android-error-handling, and android-compose-ui."

---

### Feature flag gated feature
**Skills:** `android-presentation-mvi` + `android-compose-ui` + `android-data-layer`
**Add if needed:** `android-di-koin` (flag provider injection), `android-analytics-logging` (track flag exposure), `android-testing` (test both flag states in ViewModel)
**Prompt hint:** "Gate the [name] feature behind a feature flag following android-presentation-mvi and android-data-layer."

---

### Deep link handling
**Skills:** `android-deep-links` + `android-navigation` + `android-presentation-mvi`
**Add if needed:** `android-auth-security` (guard deep links behind session), `android-analytics-logging` (track deep link opens), `android-testing` (test NavController deep link resolution)
**Prompt hint:** "Add deep link support for [destination] following android-deep-links and android-navigation."

---

### File download and sharing
**Skills:** `android-permissions-device-apis` + `android-presentation-mvi` + `android-compose-ui`
**Add if needed:** `android-workmanager` (background download), `android-error-handling` (typed download errors), `android-coroutines-flow` (progress as Flow), `android-testing` (download ViewModel tests)
**Prompt hint:** "Implement file download and sharing following android-permissions-device-apis and android-presentation-mvi."

---

### Form with validation
**Skills:** `android-text-input-forms` + `android-presentation-mvi` + `android-compose-ui`
**Add if needed:** `android-localization-accessibility` (error labels, content descriptions), `android-error-handling` (validation error types), `android-testing` (test all validation states)
**Prompt hint:** "Create a [name] form with validation following android-text-input-forms, android-presentation-mvi, and android-compose-ui."

---

### New module setup
**Skills:** `android-module-structure` + `android-build-logic-gradle` + `android-di-koin`
**Add if needed:** `android-testing` (module-level test setup), `android-obfuscation-r8` (module keep rules)
**Prompt hint:** "Set up a new :[name] module following android-module-structure and android-build-logic-gradle."

---

### CI/CD pipeline setup
**Skills:** `android-ci-cd-release` + `android-build-logic-gradle`
**Add if needed:** `android-obfuscation-r8` (release build validation), `android-testing` (test stages in pipeline), `android-analytics-logging` (build metadata)
**Prompt hint:** "Set up a CI/CD pipeline following android-ci-cd-release and android-build-logic-gradle."

---

### App performance audit
**Skills:** `android-performance-profiling` + `android-compose-ui` + `android-coroutines-flow`
**Add if needed:** `android-paging-offline-sync` (list performance), `android-image-loading-coil` (image memory), `android-room-database` (slow query analysis)
**Prompt hint:** "Audit and improve app performance following android-performance-profiling, android-compose-ui, and android-coroutines-flow."

---

### Crash and error monitoring
**Skills:** `android-analytics-logging` + `android-error-handling`
**Add if needed:** `android-coroutines-flow` (uncaught coroutine errors), `android-presentation-mvi` (surface errors as Events), `android-testing` (test error paths)
**Prompt hint:** "Add crash and error monitoring following android-analytics-logging and android-error-handling."

---

### Adaptive / tablet layout
**Skills:** `android-compose-ui` + `android-presentation-mvi` + `android-navigation`
**Add if needed:** `android-localization-accessibility` (RTL and accessibility on adaptive layouts), `android-testing` (window-size class UI tests), `android-screenshot-testing` (multi-window snapshots)
**Prompt hint:** "Create an adaptive layout for [screen] following android-compose-ui and android-navigation."

---

### Screenshot testing for a screen
**Skills:** `android-screenshot-testing` + `android-compose-ui`
**Add if needed:** `android-presentation-mvi` (snapshot each UI State variant), `android-localization-accessibility` (RTL and font-scale snapshots)
**Prompt hint:** "Add screenshot tests for [screen] following android-screenshot-testing and android-compose-ui."

---

### Full feature scaffold (all layers)
**Skills:** `android-module-structure` + `android-build-logic-gradle` + `android-data-layer` + `android-room-database` + `android-presentation-mvi` + `android-compose-ui` + `android-navigation` + `android-di-koin` + `android-error-handling` + `android-testing`
**Add if needed:** `android-paging-offline-sync` (paged data), `android-analytics-logging` (events), `android-coroutines-flow` (complex async), `android-screenshot-testing` (snapshot coverage)
**Prompt hint:** "Scaffold the complete [name] feature across all layers following android-module-structure, android-data-layer, android-presentation-mvi, android-compose-ui, android-navigation, android-di-koin, and android-testing."
