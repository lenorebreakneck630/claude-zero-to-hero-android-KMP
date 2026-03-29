# Glossary

Shared vocabulary used across all skill documents. Claude Code and Copilot use these definitions as the single source of truth for names and types.

---

## Core types

| Term | Definition |
|---|---|
| `State` | Immutable data class representing everything a screen needs to render. Held in `StateFlow`. |
| `Action` | Sealed interface of all user intents a screen can trigger. Passed to `ViewModel.onAction()`. |
| `Event` | One-shot side effect (navigate, show snackbar). Emitted via `Channel`, never stored in `State`. |
| `Result<D, E>` | Typed success/failure wrapper. `Result.Success(data)` or `Result.Error(error)`. |
| `EmptyResult<E>` | `typealias EmptyResult<E> = Result<Unit, E>`. Used when success carries no value. |
| `DataError` | Sealed interface of typed errors: `DataError.Network`, `DataError.Local`, `DataError.Auth`. |
| `UiText` | Sealed class wrapping a string resource (`StringResource`) or a raw string (`DynamicString`). Used to surface errors in UI without leaking Android context into the ViewModel. |

---

## Layer names

| Term | Definition |
|---|---|
| Domain layer | `core:domain` — models, repository interfaces, use cases. No Android dependencies. |
| Data layer | `core:data` — repository impls, DAOs, remote data sources, DTOs, mappers. |
| Presentation layer | Feature ViewModel + State/Action/Event. Depends on domain only. |
| UI layer | `@Composable` functions. Stateless except for `remember` / `collectAsStateWithLifecycle`. |

---

## Architecture patterns

| Term | Definition |
|---|---|
| Root composable | Top-level composable that injects the ViewModel, collects state, and handles events via `LaunchedEffect`. |
| Screen composable | Pure render function — takes `State` and `(Action) -> Unit`. No ViewModel, no side effects. |
| RemoteMediator | Paging 3 component that fetches from network and writes to Room, enabling offline-first paged lists. |
| DriverFactory | `expect`/`actual` class that creates the platform-specific SQLDelight `SqlDriver`. |

---

## DI terms

| Term | Definition |
|---|---|
| `networkModule` | Koin module providing `HttpClient` and API services. |
| `databaseModule` | Koin module providing `RoomDatabase` / SQLDelight database and DAOs. |
| `dataModule` | Koin module providing repository implementations. |
| `featureModule` | One Koin module per `:feature:*` module providing the feature's ViewModel(s). |

---

## Testing terms

| Term | Definition |
|---|---|
| Fake | Hand-written test double implementing a repository interface. Preferred over mocks. |
| `UnconfinedTestDispatcher` | JUnit 5 coroutine dispatcher that runs coroutines eagerly in tests. |
| Turbine | Library for testing `Flow` emissions with `test { awaitItem() }`. |
| Golden image | Baseline screenshot stored in `src/test/snapshots/`. CI fails if the diff exceeds threshold. |
