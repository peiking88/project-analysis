# Workflow Blueprints

Document representative end-to-end workflows that serve as implementation templates for similar features.

## Core Sections (Required)

### 1) Workflow Catalog

List the [WORKFLOW_COUNT] most representative workflows:

| # | Workflow name | Trigger | Entry point | Layers traversed | Business purpose |
|---|---------------|---------|-------------|------------------|------------------|
| 1 | [NAME] | [event/request/schedule] | [FILE_PATH] | [e.g., controller→service→repo→db] | [purpose] |

### 2) Workflow Traces

For each workflow in the catalog, document the full E2E path.

#### Workflow [N]: [NAME]

**Entry point:**
- File + method signature: [FILE_PATH:LINE]
- Trigger: [HTTP POST /api/..., message queue, cron schedule, user action]
- Request shape (if applicable): [DTO/input model class + key fields]

**Processing flow (trace in order):**
```
[entry] → [validation/auth] → [service/business logic] → [data access] → [response]
```
List each step with file:line evidence.

**Response shape:**
- Success response: [DTO/model class + key fields]
- Error response structure: [error model class or pattern]

**Key decisions:**
- What is validated at each layer?
- What transforms happen to the data? (DTO→Domain, Domain→Response)
- Where are side effects triggered? (events, notifications, audit logs)

### 3) Layer Implementation Patterns

Document how each layer is implemented across the traced workflows.

| Layer | Pattern | Example file | Reuse approach |
|-------|---------|-------------|----------------|
| Controller/Handler | [base class, attribute convention, DI] | [FILE_PATH] | [how to add new] |
| Validation | [attributes, FluentValidation, class-validator] | [FILE_PATH] | [how to add rules] |
| Service/Business | [interface+impl, module functions, use-case] | [FILE_PATH] | [how to add new] |
| Data mapping | [AutoMapper, manual, MapStruct] | [FILE_PATH] | [how to map new types] |
| Data access | [Repository, DAO, ORM methods] | [FILE_PATH] | [how to add queries] |
| Response building | [DTO assembly, status code selection] | [FILE_PATH] | [how to add new response] |

### 4) Error Handling Patterns

| Pattern | Where used | Evidence |
|---------|------------|----------|
| Exception types | [custom vs stdlib] | [FILE_PATH] |
| Try/catch placement | [controller-level, service-level, middleware] | [FILE_PATH] |
| Global handler | [middleware, filter, exception handler class] | [FILE_PATH] |
| Error logging | [what is logged + at what level] | [FILE_PATH] |
| Retry/circuit-breaker | [polly, resilience4j, custom] | [FILE_PATH or TODO] |

### 5) Implementation Templates

Provide reusable code templates for adding features that follow existing patterns.

**Adding a new API endpoint:**
```
1. [step — e.g., define request DTO in src/dtos/]
2. [step — e.g., add service method in src/services/]
3. [step — e.g., add controller action in src/controllers/]
4. [step — e.g., register route in src/routes/]
5. [step — run tests, check lint]
```

**Adding a new service method:**
```
[ordered steps with file paths]
```

**Adding a new data query:**
```
[ordered steps with file paths]
```

### 6) Common Pitfalls

| Pitfall | Why it happens | Where observed | How to avoid |
|---------|---------------|----------------|-------------|
| [e.g., N+1 queries in list endpoints] | [lazy loading in loop] | [FILE_PATH] | [eager fetch / batch query] |

### 7) Evidence

- [scan output section reference]
- [path/to/traced/entry-point]
- [path/to/traced/service-layer]
- [path/to/traced/data-access]
- [path/to/traced/response-construction]

## Extended Sections (Optional)

Add only when needed for complex repos:

- Sequence diagram (Mermaid format) for each traced workflow
- Async/event-driven workflow traces (message consumers, background jobs, webhooks)
- Technology-specific implementation appendix ([DOTNET.md / SPRING.md / REACT.md] — load from references/ if available)
- Feature-flag or configuration-driven behavior patterns
- Multi-service workflow traces (service A → queue → service B → DB)
- Migration/rollback workflow patterns
