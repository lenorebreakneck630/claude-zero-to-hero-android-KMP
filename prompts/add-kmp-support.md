<!--
  PROMPT: Add KMP support to an existing Android module
  USE WHEN: You want to migrate a core Android module to Kotlin Multiplatform
            so it can be shared with an iOS target.
  HOW TO USE:
    1. Open Claude Code in your Android project
    2. Paste this prompt (edit the [module] placeholder)
-->

# Add KMP support to :[module]

## Goal

Migrate `:[module]` to a Kotlin Multiplatform library module so domain models, repository interfaces, use cases, and network logic can be shared with an iOS target. Keep Android-specific implementations (Room, Firebase, Play Billing) in `androidMain`.

## Skills to apply

`android-kmp-shared-module`, `android-kmp-ktor-serialization`, `android-error-handling`, `android-coroutines-flow`

Add `android-kmp-sqldelight` if migrating away from Room.
Add `android-kmp-viewmodel` if sharing ViewModel logic with iOS.

## Steps

### 1. Convert module to KMP

Update `[module]/build.gradle.kts`:
- Replace `com.android.library` plugin with `org.jetbrains.kotlin.multiplatform` + `com.android.library`
- Add `androidTarget`, `iosX64`, `iosArm64`, `iosSimulatorArm64` targets
- Split dependencies into `commonMain`, `androidMain`, `iosMain` source sets
- Follow `android-kmp-shared-module` exactly

### 2. Audit existing code

For every file in the module, decide:

| Stays in `commonMain` | Moves to `androidMain` |
|---|---|
| Domain models (data classes) | Room `@Entity`, `@Dao` |
| Repository interfaces | Android `Context` usage |
| Use cases | Firebase calls |
| Ktor DTOs | Platform file paths |
| `kotlinx.serialization` models | Android permissions |

### 3. Fix Android-only imports

Any file with `android.*`, `androidx.*`, or `com.google.*` imports must move to `androidMain`.

Replace Android-specific types with `expect`/`actual` only when the interface must exist in `commonMain`. Prefer keeping the interface in `commonMain` and the impl in `androidMain`.

### 4. Migrate networking (if applicable)

Follow `android-kmp-ktor-serialization`:
- Move `HttpClient` factory to `commonMain`
- Inject `OkHttp` engine in `androidMain`, `Darwin` in `iosMain`
- Wrap all calls with `safeApiCall` returning `Result<T, DataError.Network>`

### 5. Migrate local storage (if applicable)

**Option A — keep Room on Android only:**
- Move repository interface to `commonMain`
- Keep `RoomDatabase`, `@Dao`, `@Entity` in `androidMain`

**Option B — migrate to SQLDelight (full KMP):**
- Follow `android-kmp-sqldelight`
- Replace Room entities with `.sq` schema files
- Replace `@Dao` with generated SQLDelight query classes

### 6. Verify

```bash
./gradlew :[module]:compileKotlinAndroid        # Android must compile
./gradlew :[module]:compileKotlinIosArm64       # iOS must compile
./gradlew :[module]:iosArm64Test                # shared tests pass
./gradlew :[module]:testDebugUnitTest           # Android tests pass
```

## Checklist

- [ ] No `android.*` imports in `commonMain` source set
- [ ] `expect`/`actual` used only where platform divergence is unavoidable
- [ ] All domain models are plain Kotlin — no annotations from Android libraries
- [ ] Ktor engine injected per platform
- [ ] Repository interface in `commonMain`, impl in `androidMain` (or `sqldelight`)
- [ ] Coroutines use KMP-compatible artifacts (`kotlinx-coroutines-core`, not `-android`)
