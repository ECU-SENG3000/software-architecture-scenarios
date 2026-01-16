# Software Architecture Tools and Technologies: A Comprehensive Catalog

A practical, categorized reference of approaches, platforms, and products architects use to design and operate modern systems. Each section lists concepts and common implementations so you can choose patterns first, then pick technology.

---

## Architectural approaches and styles

- Domain-Driven Design (DDD): strategic (bounded contexts, context maps) and tactical (entities, value objects, aggregates, repositories). Supporting practices: Event Storming, ubiquitous language.
- Layered architecture: presentation, application, domain, infrastructure.
- Hexagonal / Ports & Adapters: isolate core domain behind ports; adapters for UI, DB, integrations.
- Clean architecture / Onion architecture: dependency rule toward the center (domain).
- Modular monolith: strong internal boundaries without distributed deployment.
- Microservices: independently deployable services around business capabilities; Conway’s Law alignment.
- Service-Oriented Architecture (SOA) / ESB: coarse-grained services with centralized integration.
- Event-driven architecture (EDA): pub/sub, event sourcing, CQRS, streams-first designs.
- Serverless: function-as-a-service and managed backends.
- Data mesh: domain-oriented data ownership, self-serve data platforms, data products.
- Edge-first architectures: offload compute to CDNs/edge workers.

## Interface and integration

- API styles: REST, gRPC, GraphQL, WebSockets, Server-Sent Events (SSE), SOAP.
- Protocols & formats: HTTP/1.1, HTTP/2, HTTP/3 (QUIC), AMQP, MQTT, Kafka protocol; JSON, XML, Protocol Buffers, Avro; JSON Schema.
- API contracts: OpenAPI/Swagger, gRPC proto files, AsyncAPI, GraphQL SDL, JSON:API.
- Gateways and edge:
  - Open source: NGINX, Envoy, HAProxy, Kong, Tyk, Traefik.
  - Managed: Azure API Management, AWS API Gateway, GCP API Gateway, Apigee.
- Service mesh: Istio, Linkerd, Consul Connect, AWS App Mesh; features: mTLS, retries, circuit breaking, observability.
- ESB / iPaaS: MuleSoft, WSO2, IBM Integration Bus, Azure Logic Apps, Boomi, Workato.

## Messaging, events, and streaming

- Message brokers & queues: Apache Kafka, RabbitMQ, ActiveMQ/Artemis, NATS, Redis Streams, Pulsar; cloud: Azure Service Bus, AWS SQS/SNS, Google Pub/Sub, Amazon Kinesis.
- Stream processing: Kafka Streams, ksqlDB, Apache Flink, Apache Spark Structured Streaming, Azure Stream Analytics, Amazon Kinesis Data Analytics.
- Patterns: pub/sub, event sourcing, CQRS, saga orchestration/choreography, outbox/inbox, idempotency keys, dead-letter queues, back-pressure.

## Data storage and access

- Operational databases:
  - Relational (OLTP): PostgreSQL, MySQL/MariaDB, SQL Server, Oracle.
  - Document: MongoDB, Couchbase.
  - Key-value: Redis, DynamoDB, Aerospike.
  - Wide-column: Apache Cassandra, ScyllaDB, HBase.
  - Time-series: InfluxDB, TimescaleDB, VictoriaMetrics.
  - Graph: Neo4j, JanusGraph, Amazon Neptune.
- Search and indexing: Elasticsearch/OpenSearch, Apache Solr; full-text, aggregations; vector search: Milvus, Weaviate, Pinecone, pgvector.
- Caching:
  - Technologies: Redis, Memcached, Hazelcast, Ehcache; CDN caches.
  - Strategies: cache-aside, read-through, write-through, write-behind, refresh-ahead; HTTP caching headers (ETag, Cache-Control), client-side caches.
- Analytics & data platforms: data warehouses (Snowflake, BigQuery, Redshift, Azure Synapse); data lakes and table formats (Delta Lake, Apache Iceberg, Apache Hudi); lakehouse engines (Databricks, Apache Spark, Trino/Presto).
- Data movement & governance: ETL/ELT (dbt, Apache Airflow), CDC (Debezium, AWS DMS), replication (Kafka Connect), catalogs/lineage (DataHub, Amundsen, Collibra, OpenLineage/Marquez).

## Compute and runtime platforms

- Containers & orchestration: Docker/Podman; Kubernetes (AKS/EKS/GKE), ECS, Nomad; packaging with OCI images; Helm, Kustomize, Operators.
- Serverless compute: AWS Lambda, Azure Functions, Google Cloud Functions, Cloudflare Workers, Azure Container Apps jobs.
- Application runtimes & frameworks:
  - JVM: Spring Boot, Quarkus, Micronaut.
  - .NET: ASP.NET Core (minimal APIs, MVC), Orleans (virtual actors).
  - Node.js: Express, NestJS, Fastify, Hapi.
  - Python: Django, Flask, FastAPI, Celery for tasks.
  - Go: Gin, Echo, Fiber.
  - Ruby: Rails.
  - PHP: Laravel, Symfony.
  - Rust: Actix Web, Axum.
- Workflow engines & orchestration: Temporal, Cadence, Zeebe, Camunda, AWS Step Functions, Azure Durable Functions.
- Batch & data pipelines: Spring Batch, Apache Airflow, Azure Data Factory, AWS Glue.

## Middleware and cross-cutting concerns

- Web/app middleware examples:
  - ASP.NET Core middleware pipeline (request/response, dependency injection, filters).
  - Spring MVC filters/interceptors, Servlet filters.
  - Node.js Express/NestJS middleware, Koa; Django middleware.
- Common cross-cutting concerns: authentication/authorization, input validation, serialization, logging, correlation IDs, CORS, CSRF, compression, security headers, rate limiting, response caching.
- Resilience libraries: Polly (.NET), Resilience4j (JVM), Hystrix (legacy), Envoy rate limit service, Bucket4j.
- Feature flags & config: LaunchDarkly, Unleash, Flagsmith; centralized config via Consul, Spring Cloud Config, etcd, AWS AppConfig, Azure App Configuration.

> See also: `Importance of Middleware` for typical middleware use cases.

## Scheduling, jobs, and background processing

- Job queues and workers: Celery (Python), RQ, Huey; Sidekiq/Resque (Ruby); Bull/BullMQ (Node.js), Hangfire (C#), Laravel Queues.
- Schedulers: cron, Windows Task Scheduler; Quartz/Quartz.NET, APScheduler; Kubernetes CronJob; Temporal schedules.
- Cloud schedulers and triggers: AWS EventBridge Scheduler, CloudWatch Events; Google Cloud Scheduler; Azure Functions Timer Trigger, Azure Logic Apps Recurrence.
- Batch/ETL orchestrators: Apache Airflow (DAGs), Prefect, Dagster; Azure Data Factory pipelines.

## Observability and operations

- Logging: Elastic/ELK, OpenSearch, Loki; structured logging libs (Serilog, NLog, Logback, Winston, pino).
- Metrics: Prometheus + Grafana, StatsD/Telegraf, InfluxDB, CloudWatch metrics, Azure Monitor metrics, Datadog metrics.
- Tracing: OpenTelemetry (OTel), Jaeger, Zipkin, Grafana Tempo; vendor APMs (New Relic, Datadog, Dynatrace, AppDynamics, Azure Application Insights, AWS X-Ray).
- Alerting & SLOs: Alertmanager, Grafana alerting, OpenSLO, Nobl9, PagerDuty, Opsgenie.

## Security, identity, and compliance

- Identity & access: OAuth 2.0, OpenID Connect, SAML; JWT/JWS/JWE; mTLS; SCIM.
- Providers: Azure AD/Microsoft Entra ID, Okta, Auth0, Keycloak, AWS Cognito.
- Secrets & keys: HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager; KMS (AWS KMS, Azure Key Vault Keys); cert-manager, Let’s Encrypt.
- Perimeter & edge security: WAF (AWS WAF, Azure WAF, Cloudflare), bot protection; DDoS mitigation.
- Policy as code & governance: OPA/Gatekeeper, Kyverno, Conftest; security scanning (Snyk, Trivy, Anchore, Dependabot).
- Threat modeling & privacy: STRIDE, PASTA, LINDDUN; Microsoft Threat Modeling Tool; DLP, PII discovery and anonymization tools.

## Delivery, platforms, and infrastructure as code

- CI/CD: GitHub Actions, Azure DevOps Pipelines, GitLab CI, Jenkins, CircleCI, Argo Workflows; progressive delivery (Argo CD, Flux, Spinnaker), blue/green & canary.
- Infrastructure as Code (IaC): Terraform, Pulumi, AWS CloudFormation, Azure Bicep/ARM; config management (Ansible, Chef, Puppet). 
- image building (Packer).
- Packaging & registries: Docker/OCI registries (GHCR, ECR, ACR, GCR); language registries (npm/pnpm, Maven/Gradle, NuGet, pip/Poetry, RubyGems).
- Artifact repositories: Nexus, Artifactory, GitHub Packages.

## Networking, performance, and scalability

- Reverse proxies & load balancers: NGINX, Envoy, HAProxy; cloud LBs (AWS ALB/NLB, Azure Load Balancer, Google Cloud Load Balancer).
- CDN and edge: CloudFront, Azure Front Door, Cloudflare, Fastly, Akamai; edge compute and routing.
- Performance techniques: connection pooling, HTTP/2 & HTTP/3, gRPC, compression (gzip/brotli), image optimization (imgproxy, Thumbor).
- Resilience patterns: timeouts, retries with backoff/jitter, circuit breakers, bulkheads, rate limiting, hedged requests.

## Modeling, documentation, and governance

- System modeling: C4 model, UML, ArchiMate, BPMN.
- API documentation and contracts: OpenAPI/Swagger UI, ReDoc, AsyncAPI, GraphQL SDL and schema registries.
- Decision records and reviews: ADRs (adr-tools), architecture review templates, RFC processes.
- Catalog & developer portals: Backstage, Service Catalogs, Scorecards.

## Frontend architecture (for end-to-end systems)

- Patterns: micro frontends, SPA, SSR/SSG, edge rendering.
- Frameworks: React/Next.js, Angular, Vue/Nuxt, Svelte/SvelteKit, Blazor.
- Delivery: module federation, asset pipelines, CDN strategies, BFF (Backend for Frontend).

## AI/ML platform integrations (optional but increasingly common)

- Model serving: KServe/KFServing, Seldon, Triton, MLflow Serving, Ray Serve; cloud endpoints (SageMaker, Azure ML, Vertex AI).
- Feature stores: Feast; vector databases (Weaviate, Pinecone, Milvus, pgvector) for RAG.
- Pipelines: Kubeflow, MLflow, Metaflow, Airflow; experiment tracking and registries.

## Testing, quality, and reliability engineering

- Unit/integration testing: JUnit/TestNG, NUnit/xUnit, pytest, Jest/Mocha, Go test.
- Contract testing: Pact (consumer-driven), schema validation, MockServer, Hoverfly.
- End-to-end testing: Cypress, Playwright, Selenium.
- Load and performance testing: k6, JMeter, Gatling, Locust, Vegeta.
- Chaos and resilience testing: Chaos Mesh, Litmus, Gremlin, Toxiproxy.
- Static analysis & SCA: SonarQube, ESLint/TSLint, Pylint/flake8, SpotBugs, Checkstyle; dependency scanning (OWASP Dependency-Check, Snyk).

---

## How to choose (quick rubric)

- Start with the pattern: clarify the quality attributes (scalability, latency, reliability, cost, compliance) and the operational context.
- Prefer managed services for undifferentiated heavy lifting; self-host only with a clear need.
- Minimize technology sprawl: pick one per category where feasible (one queue, one DB per workload type, one gateway, one IaC tool).
- Favor strong contracts and observability from day one (OpenAPI/OTel), and build with resiliency primitives (timeouts, retries, circuit breakers).

## Related docs 

- Common uses of middleware: `Importance of Middleware`.
- Architecture Descriptions
- Architecture Comparisons
- Quality Attributes


