<!--
  When to use:
  You are building a brand-new feature from scratch and need all layers created together:
  a dedicated Gradle module, data sources, repository, domain model, ViewModel, Compose UI,
  navigation route, Koin wiring, and an initial test suite.

  Skills activated:
  android-module-structure, android-build-logic-gradle, android-data-layer,
  android-room-database, android-error-handling, android-presentation-mvi,
  android-compose-ui, android-navigation, android-di-koin, android-testing

  Fill in every [PLACEHOLDER] before pasting.
-->

# Scaffold new feature: [FEATURE_NAME]

Follow **android-module-structure**, **android-build-logic-gradle**, **android-data-layer**,
**android-room-database**, **android-error-handling**, **android-presentation-mvi**,
**android-compose-ui**, **android-navigation**, **android-di-koin**, and **android-testing** exactly.

## Feature overview
- Feature name: `[FEATURE_NAME]`
- Entity name: `[ENTITY_NAME]` (e.g. `Article`, `Order`, `Product`)
- API endpoint: `[API_ENDPOINT]` (e.g. `GET /api/v1/articles`)
- Local cache required: [YES / NO]
- Auth-gated: [YES / NO]

## Tasks

### 1. Module
- Create a new Gradle module at `:[FEATURE_NAME]` (or `feature:[FEATURE_NAME]`).
- Apply the shared convention plugin (`androidFeature` or equivalent).
- Declare only the dependencies this module needs; do not add `:app` as a dependency.

### 2. Data layer
- Create a DTO class `[ENTITY_NAME]Dto` with `@Serializable`.
- Create a domain model `[ENTITY_NAME]`.
- Create a mapper `[ENTITY_NAME]Dto.toDomain(): [ENTITY_NAME]`.
- Create a `[ENTITY_NAME]RemoteDataSource` using Ktor HttpClient for `[API_ENDPOINT]`.
- If local cache is required: create a Room entity `[ENTITY_NAME]Entity`, a DAO
  `[ENTITY_NAME]Dao`, and a `[ENTITY_NAME]LocalDataSource`.
- Wrap all network/database calls with the `safeCall` / `Result` helper from
  **android-error-handling**.

### 3. Repository
- Define interface `[ENTITY_NAME]Repository` in the domain layer.
- Implement `[ENTITY_NAME]RepositoryImpl` combining the data sources.
- Return `Result<[ENTITY_NAME], DataError>` from all public functions.

### 4. Presentation
- Create `[FEATURE_NAME]State`, `[FEATURE_NAME]Action`, and `[FEATURE_NAME]Event`
  following **android-presentation-mvi**.
- Create `[FEATURE_NAME]ViewModel` injecting `[ENTITY_NAME]Repository`.
- Split into `[FEATURE_NAME]Root` (stateful) and `[FEATURE_NAME]Screen` (stateless)
  following **android-compose-ui**.

### 5. Navigation
- Define a type-safe route object `[FEATURE_NAME]Route` following **android-navigation**.
- Add a `[featureName]Graph` extension on `NavGraphBuilder`.
- Wire the graph into the root nav host in `:app`.

### 6. Dependency injection
- Create a Koin module `[featureName]Module` following **android-di-koin**.
- Provide: data source(s), repository, and ViewModel.
- Include the module in the app's module list.

### 7. Tests
- Write a `[FEATURE_NAME]ViewModelTest` using **android-testing** patterns:
  JUnit5, Turbine, `UnconfinedTestDispatcher`, `Fake[ENTITY_NAME]Repository`.
- Cover: initial load success, initial load error, each `[FEATURE_NAME]Action`.

## Constraints
- Do not use `GlobalScope`.
- Do not expose Android types in the domain layer.
- All strings shown in the UI must be in `strings.xml`.
- Keep each file under 300 lines; extract helpers if needed.
