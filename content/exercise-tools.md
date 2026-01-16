

# Practical Tool Choices: simple → advanced

This sheet helps students pick pragmatic, progressively more capable tools for common architecture concerns. Each section lists simple-to-complex options, a short pros/cons summary for each option, and short scenarios where students choose the most appropriate option and justify why.

Use this in-class: give students a scenario and ask them to pick one option per section with a one-sentence rationale.

---

## 1) Persistence (storing data)

Goal: store application data reliably. Start with the simplest mechanism and move to more advanced, scalable stores.

- Plain text file (TXT)

  Pros: trivial to read/write, no extra software, easy to inspect. Good for notes, logs, tiny config.

  Cons: no structure, no concurrent writes, no queries, poor durability guarantees.

- CSV file

  Pros: structured rows/columns, human-readable, importable to spreadsheets. Good for small tabular datasets.

  Cons: no transactions, concurrent access issues, limited typing, hard to enforce constraints.

- JSON file

  Pros: supports nested documents, easy in most languages, good for configuration and small data dumps.

  Cons: same concurrency and query limitations as files; large JSON can be slow to parse.

- Spreadsheet (Excel)

  Pros: familiar to many users, easy editing, basic formulas. Good for small business data entry.

  Cons: not ideal for programmatic access at scale; concurrent editing and automation limitations.

- In-memory data structure (process memory)

  Pros: fastest access; ideal for temporary state or caches in single-process apps.

  Cons: loses data on restart; not shared across processes without extra tooling.

- Local embedded relational DB (SQLite)

  Pros: SQL support, transactions, indexes, single-file DB, zero config for small apps.

  Cons: limited concurrency for many writers; not a networked service.

- Local embedded document DB (LiteDB, Realm) / file-based JSON DB

  Pros: simple document model, no server process, good for small desktop/mobile apps.

  Cons: similar concurrency and backup limitations.

- Networked relational DB (Postgres, MySQL, SQL Server)

  Pros: strong consistency, transactions, rich query and indexing, replication and backups.

  Cons: operational complexity, administration required.

- Networked document DB (MongoDB, Couchbase)

  Pros: flexible schema, scale-out options, good for JSON-like documents.

  Cons: eventual consistency trade-offs depending on config; different query model.

- Cloud object blob storage (S3, Azure Blob)

  Pros: cheap storage for large objects, high durability, good for backups, media, large JSON blobs.

  Cons: object-store semantics (no transactions across objects), higher latency for small reads.

- Specialized stores (Time-series DB, Graph DB, Vector DB)

  Pros: optimized for specific queries (timeseries, relationships, vector similarity).

  Cons: niche use-cases; adds complexity to ops.

Teaching note: start students by mapping requirements: size, concurrency, query patterns, durability, and multi-user access. For homework, ask them to pick the smallest store that satisfies requirements.

Example student scenario — Persistence

- Scenario: "A small lab application stores sensor readings from one machine at 1 reading/second and must keep a 24-hour buffer. No network available."

  Suggested pick: JSON file or local embedded DB (SQLite) depending on query needs; CSV is OK if only row storage and simple analysis are needed.

  Rationale: single-machine, small retention, no concurrency — simple file-based storage is easiest to operate.

> .NET examples: EF Core (code-first or database-first) with SQL Server or PostgreSQL; Dapper for lightweight SQL access; Microsoft.Data.Sqlite (+ EF Core or raw ADO.NET) for local SQLite files; LiteDB for an embedded document DB; System.Text.Json for JSON file read/write; Azure Cosmos DB .NET SDK and Azure.Storage.Blobs for cloud storage scenarios.

> Python examples: SQLAlchemy (ORM and Core) with psycopg2 or asyncpg for Postgres; sqlite3 / aiosqlite for local DB files; peewee as a lightweight ORM; TinyDB or shelve for embedded stores; pandas for CSV/Excel handling; boto3 for S3 and azure-storage-blob for Azure blob storage.

---

## 2) Scheduling / Triggering / Batching

Goal: run work at times, on events, or in batches with minimal operational overhead.

- Manual / human-triggered (user clicks, run script)

  Pros: simplest, no infra. Good for occasional tasks and demos.

  Cons: unreliable for recurring tasks; needs a human.

- OS scheduler (cron on Linux, Task Scheduler on Windows)

  Pros: ubiquitous and simple, good for nightly jobs, backups, small batch tasks.

  Cons: limited observability and orchestration; hard to manage many jobs across machines.

- Application-level scheduler (APScheduler, Quartz)

  Pros: embed scheduling in your app with programmatic control and retry hooks.

  Cons: requires app to be running; single-process limitations unless clustered.

- Container scheduler cron (Kubernetes CronJob)

  Pros: integrates with containerized deployments; uses cluster scheduling and logs.

  Cons: Kubernetes complexity; overkill for tiny apps.

- Serverless / managed schedulers (AWS EventBridge Scheduler, Cloud Scheduler)

  Pros: fully managed, high reliability, easy to scale timers and cron-like tasks.

  Cons: cloud lock-in; cost considerations.

- Workflow orchestrators (Airflow, Prefect, Temporal for durable workflows)

  Pros: DAGs, retries, dependencies, visibility, retries, and complex data pipelines.

  Cons: heavier to operate; best for complex ETL or multi-step business workflows.

Example student scenario — Scheduling

- Scenario: "Export a small CSV report every morning at 03:00 and email it to HR. Failures should notify admin."

  Suggested pick: OS scheduler (cron) or small app-level scheduler that triggers an email job; add simple logging and alerting.

  Rationale: simple periodic task; no need for Airflow.

> .NET examples: Hangfire for recurring background jobs with a dashboard and retries; Quartz.NET for advanced in-process scheduling; ASP.NET Core background services (IHostedService) for lightweight timers; Azure Functions TimerTrigger or Durable Functions for serverless scheduling; Windows Task Scheduler for Windows hosts.

> Python examples: APScheduler for in-process cron-like scheduling; Celery beat for periodic tasks with Celery workers; RQ (Redis Queue) with rq-scheduler for simple schedules; Prefect or Airflow for workflow scheduling; azure-functions-python-worker for Azure Functions timers.

---

## 3) Clients (how users interact)

Goal: choose how users access your application: CLI, GUI, web, mobile, etc.

- Command-line interface (CLI)

  Pros: low dependencies, great for power users/ops, easy to script.

  Cons: steep learning curve for non-technical users.

- Desktop GUI (native app)

  Pros: rich native UI, offline-first capabilities.

  Cons: distribution and update challenges (see middleware and deployment); platform differences.

- Spreadsheet as UI (Excel + macros)

  Pros: familiar to business users and quick to prototype data-driven tasks.

  Cons: hard to secure and scale; manual editing risks.

- Web UI (single-page app or server-rendered pages)

  Pros: accessible on any browser, easy to update, central deployment.

  Cons: needs hosting, network access, cross-browser testing.

- Mobile app (native or PWA)

  Pros: access on phones, push notifications, offline support with sync.

  Cons: app stores, updates, platform fragmentation.

Teaching note: match client complexity to audience: internal users often prefer web UIs; power users prefer CLIs.

Example student scenario — Clients

- Scenario: "Internal admins need a simple dashboard to approve entries; non-technical HR staff must also view reports."

  Suggested pick: Web UI (server-rendered or simple SPA) for HR; optionally a CLI for admins.

  Rationale: web is familiar, centrally updated, and supports both audiences.

> .NET examples: Blazor (Server or WebAssembly) or ASP.NET Core MVC / Razor Pages for web UIs; WPF or WinForms for native desktop UIs; .NET MAUI for cross-platform mobile/desktop apps; simple .NET console applications for CLIs.

> Python examples: Django or Flask for server-rendered or API-driven web UIs; FastAPI for modern async APIs and interactive docs; Streamlit or Dash for quick data-focused UIs; tkinter, PyQt, or Kivy for simple desktop apps; click or Typer for CLIs.

---

## 4) Services and compute (how logic runs)

Goal: pick a runtime model for your app logic: scripts, single process, multiple services, or serverless.

- Single script / monolithic process

  Pros: easiest to write and deploy for very small apps; low ops.

  Cons: harder to scale and evolve; deployments affect all features.

- Multiple processes on one host

  Pros: better separation of concerns; can restart individual components.

  Cons: still operationally simple but manual wiring required.

- Containers on a host

  Pros: isolation and reproducible environments; easier to deploy multiple services.

  Cons: need container tooling and image registries.

- Orchestrated containers (Kubernetes, Docker Swarm)

  Pros: scaling, self-healing, service discovery.

  Cons: operational overhead and learning curve.

- Serverless functions (Lambda, Azure Functions)

  Pros: minimal ops, pay-per-use, auto-scaling for events.

  Cons: cold start latency, limited duration, vendor lock-in.

Teaching note: start with a monolith for small teams; move to services when team size or scalability needs grow.

Example student scenario — Services

- Scenario: "A small campus tool used by up to 25 staff concurrently; occasional spikes during enrollment week."

  Suggested pick: single process or containerized monolith with a simple autoscale plan (add more instances behind a load balancer) if using cloud.

  Rationale: small team and modest load — keep operations simple.

> .NET examples: ASP.NET Core Web API for HTTP-based services; gRPC services with Grpc.AspNetCore for efficient internal RPC; ASP.NET Core minimal APIs for tiny endpoints; Generic Host Worker Services (IHostedService) for background processors; Azure Functions for serverless event-driven compute; containerize using official .NET images (mcr.microsoft.com/dotnet/aspnet and mcr.microsoft.com/dotnet/sdk).

> Python examples: Flask or Django for WSGI apps; FastAPI for ASGI and async-first APIs (uvicorn); gRPC with grpcio for RPC; Celery, RQ, or Dramatiq for background workers; serverless via AWS Lambda (python runtime) or Azure Functions (Python worker); containerize using official Python images (python:3.11-slim).

---

## 5) Communication protocols and integration

Goal: decide how components talk to each other and external systems.

- Local function calls / in-process method calls

  Pros: fastest and simplest for monoliths.

  Cons: not usable across processes or network.

- Files / shared storage (write a file, another process reads it)

  Pros: easy decoupling for simple batch jobs.

  Cons: eventual consistency and coordination complexity.

- HTTP/REST (synchronous)

  Pros: ubiquitous, human-readable, easy to debug with curl/browser.

  Cons: synchronous dependency; latency and failure propagation must be managed.

- gRPC / binary RPC

  Pros: efficient, strong typing via proto, good for low-latency internal APIs.

  Cons: more tooling and learning curve; not as simple for browsers without proxies.

- Message queues / async messaging (RabbitMQ, Kafka, SQS)

  Pros: decouples services, enables retries and buffering, resilient to temporary outages.

  Cons: operational complexity and harder end-to-end tracing.

- Webhooks / event callbacks

  Pros: push-based integration, low polling load.

  Cons: requires public endpoint and security/verification.

Teaching note: prefer synchronous HTTP for simple request/response; use messaging when decoupling, buffering, or retries are essential.

Example student scenario — Communication

- Scenario: "When a payment post happens, you must update the accounting system and send a receipt. Accounting is occasionally down for maintenance."

  Suggested pick: publish an event to a durable queue; have receivers process accounting updates asynchronously with retries and DLQ; send receipt via an email worker.

  Rationale: decoupling avoids lost requests during accounting downtime.

> .NET examples: use HttpClient / IHttpClientFactory and ASP.NET Core for REST integrations; gRPC with .NET (Grpc.AspNetCore) for strongly-typed RPC; SignalR for real-time notifications; MassTransit or NServiceBus as higher-level messaging abstractions; Azure.Messaging.ServiceBus for Azure Service Bus, RabbitMQ.Client for RabbitMQ, Confluent.Kafka (or KafkaNET) for Kafka clients.

> Python examples: requests or httpx for HTTP clients; aiohttp for async HTTP servers/clients; grpcio for gRPC; websockets or python-socketio for real-time; kombu or Celery for messaging patterns; azure-servicebus or azure-messaging-servicebus for Azure Service Bus; pika for RabbitMQ; confluent-kafka-python or kafka-python for Kafka.

---

## 6) Caching & performance levers

Goal: improve read performance and reduce load with caches and precomputation.

- In-process cache (in memory)

  Pros: fastest for single-process apps; no extra infra.

  Cons: not shared across processes; lost on restart.

- Local file or memory-mapped cache

  Pros: persists across restarts and simple to implement.

  Cons: limited scalability.

- Distributed cache (Redis, Memcached)

  Pros: shared cache across processes and machines; good TTL semantics and eviction policies.

  Cons: operational dependency; eventual consistency.

- Materialized views / denormalized tables

  Pros: precomputed results for complex queries; very fast reads.

  Cons: complexity in keeping them fresh; extra storage cost.

Teaching note: add caching only after measuring hotspots; emphasize invalidation strategies.

> .NET examples: Microsoft.Extensions.Caching.Memory (IMemoryCache) for in-process caches; IDistributedCache implementations for Redis (Microsoft.Extensions.Caching.StackExchangeRedis) or Memcached; StackExchange.Redis for full Redis features; use ResponseCaching middleware in ASP.NET Core for simple HTTP response caching.

> Python examples: functools.lru_cache or cachetools for in-process caching; diskcache for persistent local caches; redis-py or aioredis for Redis-backed distributed caching; Flask-Caching or django-redis for framework integration; use cache headers and FastAPI response caching middlewares.

---

## 7) Observability & operations

Goal: detect, understand, and respond to problems.

- Console output / local logs

  Pros: simple to start; useful in development.

  Cons: poor for distributed systems or team ops.

- Centralized logging (ELK, Loki)

  Pros: search and correlate logs; retention and access control.

  Cons: costs and infra to operate.

- Metrics + dashboards (Prometheus + Grafana)

  Pros: alerting, SLOs, visualize trends.

  Cons: requires instrumentation and alert design.

- Distributed tracing (OpenTelemetry, Jaeger)

  Pros: trace transactions across services; essential for microservices debugging.

  Cons: instrumentation effort and storage cost.

Teaching note: start with logs and a few business metrics; add tracing when services are distributed.

> .NET examples: Microsoft.Extensions.Logging with Serilog or NLog for structured logs; Application Insights .NET SDK for hosted telemetry; OpenTelemetry .NET SDK for traces and metrics with OTLP exporters; prometheus-net or other exporters for Prometheus metrics; use Azure Monitor when running on Azure.

> Python examples: Python's logging module combined with structlog for structured logs; Sentry (sentry-sdk) for error aggregation; OpenTelemetry Python SDK for traces/metrics; prometheus_client for metrics exposition; use azure-monitor-opentelemetry or Application Insights SDK for Python on Azure.

---

## 8) Security & identity (brief)

- Simple API keys or basic auth (for internal, low-risk tools)

    Pros: trivial to implement.

    Cons: limited auditing and rotation policies.

- OAuth2 / OIDC via identity provider (Azure AD, Okta)

    Pros: single sign-on, role management, modern flows.

    Cons: setup complexity but recommended for production.

- Secrets manager (Vault, Key Vault)

    Pros: central secret lifecycle and rotation.

    Cons: another dependency to operate.

Teaching note: always protect secrets and use TLS for transport.

> .NET examples: ASP.NET Core Identity for local user/account management; Microsoft.Identity.Web to integrate Azure AD / Microsoft Entra for OIDC flows; Duende IdentityServer (or community forks) for OpenID Connect; Azure.Security.KeyVault.Secrets for Key Vault access; Microsoft.IdentityModel packages for JWT validation.

> Python examples: Django's authentication system or Flask-Login for simple auth; Authlib or PyJWT / python-jose for JWT handling; msal for Azure AD integration; auth0-python for Auth0; hvac for HashiCorp Vault; azure-identity and azure-keyvault-secrets for Azure Key Vault access.

---

## Quick student exercises (pick one or more)


1. Small Retail Scenario (10 minutes)

    - Problem: a single-store POS needs to record sales offline and sync once a day.

    - From the sections above, pick: persistence, scheduling, client, service, communication protocol, caching.

    - Suggested answers: persistence = local embedded DB or CSV, scheduling = daily sync via cron, client = desktop app or web app with offline cache, service = occasional sync script, communication = HTTP upload of batch, caching = none or local in-memory for UI.

    - .NET-specific: persistence = Microsoft.Data.Sqlite + EF Core or LiteDB for embedded/local storage; scheduling = Hangfire or an ASP.NET Core background IHostedService; client = WPF or Blazor Hybrid app for offline-capable UI; service = small ASP.NET Core Worker Service or console sync app; communication = HttpClient for batch upload; caching = IMemoryCache for the UI.

    - Python-specific: persistence = sqlite3 or SQLAlchemy with a local SQLite file; scheduling = APScheduler or a small sync script run by cron/Task Scheduler; client = PyQt or Kivy desktop app or a small Flask UI with local cache; service = lightweight Python worker (asyncio or simple script); communication = requests/httpx to POST batches; caching = in-process dict or diskcache for persistence.

1. University Lab Data (15 minutes)

    - Problem: lab PCs produce small JSON logs every minute. Central analytics will run hourly. Network is intermittent.

    - Suggested answers: persistence = local JSON or SQLite with append; scheduling = OS scheduler to push hourly (with retry); client = lightweight desktop script; communication = queue or HTTP batch upload when online; caching = local buffer.

    - .NET-specific: persistence = Microsoft.Data.Sqlite or JSON files with System.Text.Json; scheduling = Windows Task Scheduler or a small .NET console app scheduled by Task Scheduler; client = .NET console app or lightweight WPF app; communication = Azure Service Bus (Azure.Messaging.ServiceBus) or HTTP batch uploads via HttpClient; caching = local memory buffer.

    - Python-specific: persistence = sqlite3 or JSON files with the json module; scheduling = cron or Windows Task Scheduler running a Python script, or APScheduler for in-process scheduling; client = Python console script or small Tkinter/PyQt app; communication = azure-servicebus or requests/httpx for batching; caching = local memory buffer or diskcache.

1. Simple Web Form for HR (10 minutes)

    - Problem: HR needs a web form to accept employee updates and allow admins to approve changes.

    - Suggested answers: persistence = networked relational DB; client = web UI; services = monolith or single process; communication = HTTP/REST; scheduling = none; caching = basic page-cache; observability = logs + basic metrics.

    - .NET-specific: persistence = SQL Server or Postgres with EF Core; client = ASP.NET Core MVC or Blazor Server; services = ASP.NET Core monolith or minimal APIs; communication = HTTP using ASP.NET Core with IHttpClientFactory; caching = IDistributedCache with Redis; observability = Serilog + Application Insights.

    - Python-specific: persistence = Postgres with SQLAlchemy or Django ORM; client = Django templates or a FastAPI + simple frontend; services = Django or Flask monolith or FastAPI app; communication = requests/httpx; caching = django-redis or Flask-Caching with Redis; observability = Python logging + Sentry + prometheus_client.

---

## How to use this sheet in class

- Ask students to justify the *simplest* option that satisfies requirements. Encourage them to call out operational costs: backups, updates, concurrency, and monitoring.

- For each chosen option have students list 2 failure modes and one mitigation.

Last updated: 2025-11-03

