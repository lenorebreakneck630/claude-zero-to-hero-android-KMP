<!--
  When to use:
  You need to create a repository (or data source) for a specific entity.
  This prompt covers: domain interface, DTOs, mappers, typed Result errors, and either
  a Room (local), Ktor (remote), or combined implementation.

  Skills activated:
  android-data-layer, android-error-handling, android-room-database (if local),
  android-di-koin, android-testing

  Fill in every [PLACEHOLDER] before pasting.
-->

# Create repository: [ENTITY_NAME]Repository

Follow **android-data-layer**, **android-error-handling**,
[IF LOCAL OR BOTH: **android-room-database**,] and **android-di-koin** exactly.

## Repository overview
- Entity name: `[ENTITY_NAME]` (e.g. `Article`, `UserProfile`, `Transaction`)
- Data source type: `[DATA_SOURCE_TYPE]` — one of: `local`, `remote`, or `both`
- Remote endpoint(s): `[ENDPOINTS]` (e.g. `GET /api/v1/articles`, `POST /api/v1/articles`)
  (leave blank if local-only)
- Local table name: `[TABLE_NAME]` (e.g. `articles`)
  (leave blank if remote-only)
- Module: `:[MODULE_NAME]`

## Tasks

### 1. Domain model
- Create `data class [ENTITY_NAME](...)` in the domain layer (no Android imports, no
  Kotlinx annotations).

### 2. DTOs and mappers (remote)
Skip this section if `DATA_SOURCE_TYPE = local`.
- Create `@Serializable data class [ENTITY_NAME]Dto(...)` mirroring the API response.
- Create mapper `fun [ENTITY_NAME]Dto.toDomain(): [ENTITY_NAME]`.

### 3. Room entity and DAO (local)
Skip this section if `DATA_SOURCE_TYPE = remote`.
- Create `@Entity(tableName = "[TABLE_NAME]") data class [ENTITY_NAME]Entity(...)`.
- Create mapper `fun [ENTITY_NAME]Entity.toDomain(): [ENTITY_NAME]` and
  `fun [ENTITY_NAME].toEntity(): [ENTITY_NAME]Entity`.
- Create `@Dao interface [ENTITY_NAME]Dao` with:
  - `@Query("SELECT * FROM [TABLE_NAME]") fun getAll(): Flow<List<[ENTITY_NAME]Entity>>`
  - `@Upsert suspend fun upsertAll(items: List<[ENTITY_NAME]Entity>)`
  - `@Query("DELETE FROM [TABLE_NAME]") suspend fun deleteAll()`

### 4. Data sources
- If remote: create `[ENTITY_NAME]RemoteDataSource` using Ktor `HttpClient`.
  Wrap each call in `safeCall { }` from **android-error-handling** to return
  `Result<T, DataError.Network>`.
- If local: create `[ENTITY_NAME]LocalDataSource` wrapping `[ENTITY_NAME]Dao`.
  Wrap inserts/deletes in `safeCall { }` to return `EmptyResult<DataError.Local>`.

### 5. Repository interface
- Define in the domain layer:
  ```kotlin
  interface [ENTITY_NAME]Repository {
      fun get[ENTITY_NAME]s(): Flow<Result<List<[ENTITY_NAME]>, DataError>>
      suspend fun sync(): EmptyResult<DataError>
      // add further methods as needed for [ENDPOINTS]
  }
  ```

### 6. Repository implementation
- Create `[ENTITY_NAME]RepositoryImpl` in the data layer implementing the interface.
- For `both`: read from local, trigger remote sync, write remote results into local,
  emit local flow (offline-first pattern from **android-data-layer**).
- Return typed `Result<T, DataError>` everywhere; never throw across layer boundaries.

### 7. Koin wiring
- Add bindings to the feature Koin module following **android-di-koin**:
  - `single { [ENTITY_NAME]RemoteDataSource(get()) }` (if remote)
  - `single { [ENTITY_NAME]LocalDataSource(get()) }` (if local)
  - `single<[ENTITY_NAME]Repository> { [ENTITY_NAME]RepositoryImpl(get(), get()) }`

### 8. Tests
- Write `[ENTITY_NAME]RepositoryImplTest` using **android-testing**:
  - Use a fake DAO and/or a MockEngine for Ktor.
  - Assert success path returns correct domain models.
  - Assert each `DataError` variant is surfaced correctly.
  - For offline-first: assert stale data is emitted before fresh data.

## Constraints
- The domain layer must not import Room, Ktor, or any Android framework class.
- Never use `runCatching` without mapping to a typed `DataError`.
- All database operations must run on `Dispatchers.IO`; inject the dispatcher.
