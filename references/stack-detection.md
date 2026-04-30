# Stack Detection Reference

Load this file when the tech stack is ambiguous — e.g., multiple manifest files present, unfamiliar file extensions, or no obvious `package.json` / `go.mod`.

---

## Manifest File → Ecosystem

| File | Ecosystem | Key fields to read |
|------|-----------|--------------------|
| `package.json` | Node.js / JavaScript / TypeScript | `dependencies`, `devDependencies`, `scripts`, `main`, `type`, `engines` |
| `go.mod` | Go | Module path, Go version, `require` block |
| `requirements.txt` | Python (pip) | Package list with pinned versions |
| `Pipfile` | Python (pipenv) | `[packages]`, `[dev-packages]`, `[requires]` python version |
| `pyproject.toml` | Python (poetry / uv / hatch) | `[tool.poetry.dependencies]`, `[project]`, `[build-system]` |
| `setup.py` / `setup.cfg` | Python (setuptools, legacy) | `install_requires`, `python_requires` |
| `Cargo.toml` | Rust | `[dependencies]`, `[[bin]]`, `[lib]` |
| `pom.xml` | Java / Kotlin (Maven) | `<dependencies>`, `<artifactId>`, `<groupId>`, `<java.version>` |
| `build.gradle` / `build.gradle.kts` | Java / Kotlin (Gradle) | `dependencies {}`, `sourceCompatibility` |
| `composer.json` | PHP | `require`, `require-dev` |
| `Gemfile` | Ruby | `gem` declarations, `ruby` version constraint |
| `mix.exs` | Elixir | `deps/0`, `elixir: "~> X.Y"` |
| `pubspec.yaml` | Dart / Flutter | `dependencies`, `dev_dependencies`, `environment.sdk` |
| `*.csproj` | .NET / C# | `<PackageReference>`, `<TargetFramework>` |
| `*.sln` | .NET solution | References multiple `.csproj` projects |
| `deno.json` / `deno.jsonc` | Deno (TypeScript runtime) | `imports`, `tasks` |
| `bun.lockb` | Bun (JavaScript runtime) | Binary lockfile — check `package.json` for deps |
| `bunfig.toml` | Bun (configuration) | `[install]`, `[test]` sections |

---

## Language Runtime Version Detection

| Language | Where to find the version |
|----------|--------------------------|
| Node.js | `.nvmrc`, `.node-version`, `engines.node` in `package.json`, Docker `FROM node:X` |
| Bun | `bun.lockb` (binary), `bunfig.toml`, Docker `FROM oven/bun:X` |
| Python | `.python-version`, `pyproject.toml [requires-python]`, Docker `FROM python:X` |
| Go | First line of `go.mod` (`go 1.21`) |
| Java | `<java.version>` in `pom.xml`, `sourceCompatibility` in `build.gradle`, Docker `FROM eclipse-temurin:X` |
| Ruby | `.ruby-version`, `Gemfile` `ruby 'X.Y.Z'` |
| Rust | `rust-toolchain.toml`, `rust-toolchain` file |
| .NET | `<TargetFramework>` in `.csproj` (e.g., `net8.0`) |

---

## Framework Detection (Node.js / TypeScript)

| Dependency in `package.json` | Framework |
|-----------------------------|-----------|
| `express` | Express.js (minimal HTTP server) |
| `fastify` | Fastify (high-performance HTTP server) |
| `next` | Next.js (SSR/SSG React — check for `pages/` or `app/` directory) |
| `nuxt` | Nuxt.js (SSR/SSG Vue) |
| `@nestjs/core` | NestJS (opinionated Node.js framework with DI) |
| `koa` | Koa (middleware-focused, no built-in router) |
| `@hapi/hapi` | Hapi |
| `@trpc/server` | tRPC (type-safe API without REST/GraphQL schemas) |
| `routing-controllers` | routing-controllers (decorator-based Express wrapper) |
| `typeorm` | TypeORM (SQL ORM with decorators) |
| `prisma` | Prisma (type-safe ORM, check `prisma/schema.prisma`) |
| `mongoose` | Mongoose (MongoDB ODM) |
| `sequelize` | Sequelize (SQL ORM) |
| `drizzle-orm` | Drizzle (lightweight SQL ORM) |
| `react` without `next` | Vanilla React SPA (check for `react-router-dom`) |
| `vue` without `nuxt` | Vanilla Vue SPA |
| `svelte` | Svelte (check for `svelte.config.js` or `vite.config.ts` with Svelte plugin) |
| `@sveltejs/kit` | SvelteKit (full-stack Svelte — check for `svelte.config.js`) |
| `solid-js` | SolidJS (fine-grained reactivity — check for `vite.config.ts` with Solid plugin) |
| `astro` | Astro (content-focused, multi-framework — check for `astro.config.mjs`) |
| `@remix-run/react` | Remix (React SSR with nested routes — check for `remix.config.js`) |
| `vitest` | Vitest (Vite-native test runner — check `vitest.config.ts`) |
| `@playwright/test` | Playwright (E2E browser testing — check `playwright.config.ts`) |

---

## Framework Detection (Python)

| Package | Framework |
|---------|-----------|
| `fastapi` | FastAPI (async REST, auto OpenAPI docs) |
| `flask` | Flask (minimal WSGI web framework) |
| `django` | Django (batteries-included, check `settings.py`) |
| `starlette` | Starlette (ASGI, often used as FastAPI base) |
| `aiohttp` | aiohttp (async HTTP client and server) |
| `sqlalchemy` | SQLAlchemy (SQL ORM; check for `alembic` migrations) |
| `alembic` | Alembic (SQLAlchemy migration tool) |
| `pydantic` | Pydantic (data validation; core to FastAPI) |
| `celery` | Celery (distributed task queue) |

---

## Monorepo Detection

Check these signals in order:

1. `pnpm-workspace.yaml` — pnpm workspaces
2. `lerna.json` — Lerna monorepo
3. `nx.json` — Nx monorepo (also check `workspace.json`)
4. `turbo.json` — Turborepo
5. `rush.json` — Rush (Microsoft monorepo manager)
6. `moon.yml` — Moon
7. `package.json` with `"workspaces": [...]` — npm/yarn workspaces
8. Presence of `packages/`, `apps/`, `libs/`, or `services/` directories with their own `package.json`

If monorepo is detected: each workspace may have **independent** dependencies and conventions. Map each sub-package separately in `STACK.md` and note the monorepo structure in `STRUCTURE.md`.

---

## TypeScript Path Alias Detection

If `tsconfig.json` has a `paths` key, imports with non-relative prefixes are aliases. Map them before documenting structure.

```json
// tsconfig.json example
"paths": {
  "@/*": ["./src/*"],
  "@components/*": ["./src/components/*"],
  "@utils/*": ["./src/utils/*"]
}
```

Imports like `import { foo } from '@/utils/bar'` resolve to `src/utils/bar`. Document as `src/utils/bar`, not `@/utils/bar`.

---

## Docker Base Image → Runtime

If no manifest file is present but a `Dockerfile` exists, the `FROM` line reveals the runtime:

| FROM line pattern | Runtime |
|------------------|---------|
| `FROM node:X` | Node.js X |
| `FROM python:X` | Python X |
| `FROM golang:X` | Go X |
| `FROM eclipse-temurin:X` | Java X (Eclipse Temurin JDK) |
| `FROM mcr.microsoft.com/dotnet/aspnet:X` | .NET X |
| `FROM ruby:X` | Ruby X |
| `FROM rust:X` | Rust X |
| `FROM alpine` (alone) | Check what's installed via `RUN apk add` |

---

## Technology-Specific Implementation Patterns

Use this section during Phase 2 (Workflow investigation) to recognize layer conventions in common stacks. When tracing workflows, check for these signals to identify how each layer is implemented.

### .NET / ASP.NET Core

| Layer | Signal files/patterns | What to document for WORKFLOWS.md |
|-------|----------------------|-----------------------------------|
| Entry point | `*Controller.cs` with `[ApiController]`, `[Route]` attributes | Controller class + action method signature + attributes |
| Validation | `FluentValidation`, `[Required]`/`[MaxLength]` data annotations, `IValidatableObject` | Validator class or inline attribute rules |
| Service | `*Service.cs` with interface `I*Service.cs` | Interface + implementation, DI registration in `Program.cs` |
| Data mapping | `AutoMapper` profiles (`*Profile.cs`), `Mapster`, manual `.Map()` extensions | Mapping profile class or manual mapping methods |
| Data access | `*Repository.cs`, `DbContext` (EF Core), `DbSet<T>`, Dapper `SqlConnection` | Repository interface + EF queries or SQL statements |
| Error handling | `[ExceptionFilter]`, `UseExceptionHandler()`, custom `Middleware` | Global handler registration + custom exception types |
| DI registration | `Program.cs` / `Startup.cs` — `builder.Services.AddScoped<T>` | Service lifetime patterns |

### Java / Spring Boot

| Layer | Signal files/patterns | What to document for WORKFLOWS.md |
|-------|----------------------|-----------------------------------|
| Entry point | `*Controller.java` with `@RestController`, `@RequestMapping` | Controller class + method + annotations |
| Validation | `@Valid`, `@NotNull`/`@Size`, custom `Validator` beans | Annotation-based validation on DTO fields |
| Service | `*Service.java` + `*ServiceImpl.java`, `@Service`, `@Transactional` | Interface + impl + transaction boundaries |
| Data mapping | `MapStruct` (`*Mapper.java`), `ModelMapper`, `BeanUtils.copyProperties` | Mapper interface (MapStruct) or manual conversion |
| Data access | `*Repository.java` extending `JpaRepository`, `@Query`, `EntityManager` | Repository interface + custom queries |
| Error handling | `@ControllerAdvice`, `@ExceptionHandler`, `ResponseEntityExceptionHandler` | Global advice class + exception→response mapping |
| Bean config | `@Configuration`, `@Bean`, `application.yml` / `application.properties` | Configuration classes + property bindings |

### React / Next.js

| Layer | Signal files/patterns | What to document for WORKFLOWS.md |
|-------|----------------------|-----------------------------------|
| Entry point | Page components in `pages/` or `app/`, route config, `useRouter` | Component file + route path |
| API client | `fetch()` wrapper, `axios` instance, `react-query`/`@tanstack/react-query` hooks | API service module + base URL config |
| State | `useState`, `useReducer`, `useContext`, Redux store, Zustand store | State shape + update flow |
| Form handling | `react-hook-form`, `Formik`, controlled inputs | Form component + validation schema |
| Error handling | Error boundaries, `try/catch` in async handlers, toast notifications | Error component + error surfacing pattern |
| Data fetching | `getServerSideProps`, `getStaticProps`, `useEffect` + fetch, react-query `useQuery` | Data fetching strategy per route |

### Node.js / Express / Fastify

| Layer | Signal files/patterns | What to document for WORKFLOWS.md |
|-------|----------------------|-----------------------------------|
| Entry point | `routes/`, `controllers/`, `app.ts` / `server.ts` | Route definition + handler function |
| Validation | `joi`, `zod`, `class-validator`, inline checks | Schema definition or validation middleware |
| Service | `services/` directory, exported functions or classes | Service function signature + DI pattern |
| Data mapping | Manual spread/rest, `class-transformer`, lodash `_.pick` | Mapping functions or transformer usage |
| Data access | `prisma` client, `typeorm` repositories, `knex` query builder, raw `pg`/`mysql2` | ORM client usage or query builder patterns |
| Error handling | Express error middleware `(err, req, res, next)`, Fastify `setErrorHandler` | Error middleware + error response shape |
| Middleware | Auth middleware, request logging, rate limiting | Middleware chain order + purpose |

### Python / FastAPI / Flask / Django

| Layer | Signal files/patterns | What to document for WORKFLOWS.md |
|-------|----------------------|-----------------------------------|
| Entry point | FastAPI `@app.get`, Flask `@app.route`, Django `urls.py` + `views.py` | Route decorator + view/endpoint function |
| Validation | Pydantic `BaseModel` (FastAPI), `marshmallow`, Django Forms/Serializers | Schema class + field definitions |
| Service | `services.py` module, Django service classes, use-case functions | Service function signature + dependency injection |
| Data mapping | Pydantic `.model_dump()`, DRF Serializer `.data`, manual dict mapping | Serialization/deserialization pattern |
| Data access | SQLAlchemy `Session`, Django ORM `Model.objects`, `asyncpg` | ORM query patterns or raw SQL |
| Error handling | FastAPI exception handlers, Django middleware, Flask error handlers | Exception handler registration + error response shape |
