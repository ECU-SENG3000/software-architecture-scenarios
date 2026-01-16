# Teacher's Guide — Introductory Software Architecture Scenarios

Purpose: Provide instructors with concise suggested answers, key teaching points, and short activity timings for each scenario in `scenarios-introductory.md`. Use this as a facilitator's crib-sheet during class discussions or quick grading guidance.

General guidance for instructors

- Format: each scenario suggests a 10–20 minute discussion or a 15-minute small-group exercise.
- Make trade-offs explicit: emphasize why a simple solution is often better for small scopes (fewer moving parts, lower ops cost).
- Encourage students to name quality attributes first (e.g., deployability, security) and let those guide choices.
- Ask follow-ups: "What could go wrong? How would you detect and recover?" to drive operational thinking.

---

## Scenario 1: Internal Desktop Application — keeping clients up to date

Estimated time: 10–15 minutes
Learning objectives:

- Understand deployment/update trade-offs for desktop apps.
- Learn simple techniques for automated updates and rollback.

Suggested answers / key points:

- Preferred simple options: web-based app (if feasible) or centrally-managed deployment (Group Policy / MSI for Windows environments) to avoid per-desktop installs.
- Auto-update installers (with code signing) or using a package manager is a reasonable middle ground; ClickOnce or an auto-updater (Sparkle, Squirrel) are options.
- For corporate Windows shops, use AD Group Policy, SCCM, or Intune to push installs/updates.
- Rollback: keep old installers available and have version pinning; test updates on a pilot group first.
- Security: sign executables, restrict install permissions, use TLS for downloads; consider firewall rules and internal update mirrors for bandwidth control.
- Offline/slow networks: provide standalone installers, delta updates, or a local update server/CDN.

Instructor notes:

- If students jump to microservices or cloud-native solutions, steer them back to the core problem: distribution and updates.
- Ask them to name concrete operational steps (who runs updates? how do you test?).

---

## Scenario 2: Single-server Intranet App for HR

Estimated time: 10 minutes
Learning objectives:

- Choose minimal safe hosting options for internal apps.
- Identify basic security measures for intranet-only services.

Suggested answers / key points:

- Minimal architecture: one VM or a small PaaS instance + relational DB. Keep the architecture simple (monolith) for ease of support.
- Secure with network ACLs (internal network only), VPN, or reverse proxy accessible only on internal IPs. Use AD/LDAP for authentication/authorization.
- Backups: schedule regular DB backups; store copies offsite or on different physical storage; test restores occasionally.

Instructor notes:

- Emphasize least privilege: only open required ports and use company SSO where possible.
- For grading, give points for backup frequency, authentication strategy, and simplicity.

---

## Scenario 3: Simple CRUD Inventory Web App

Estimated time: 10–15 minutes
Learning objectives:

- Choose an appropriate architecture size for small-scale apps.
- Select a suitable database type and deployment pattern.

Suggested answers / key points:

- Monolith is appropriate: smaller operational burden, easier to test, deploy, and reason about for low concurrency.
- Database: relational DB (Postgres, MySQL) fits CRUD and integrity constraints; document DB possible if schema is flexible.
- Deployment: simple CI pipeline to build and deploy, one server or small container host; prefer automated backups and easy rollback/versioning.

Instructor notes:

- Award points for rationale (why not microservices) and consideration of operational costs.

---

## Scenario 4: Need to Send Emails Reliably

Estimated time: 10 minutes
Learning objectives:

- Understand background job patterns and idempotency for external side effects.
- Identify managed services vs self-hosted trade-offs.

Suggested answers / key points:

- Delegate email sending to background jobs (worker queues) so user requests are fast and resilient.
- Use an at-least-once delivery with idempotency keys to prevent duplicates; record delivered status or use outbox pattern.
- Use third-party transactional email services (SES, SendGrid) for deliverability; or a hardened SMTP relay if required.
- Monitor delivery failures, use DLQ for undeliverable messages and alerts for persistent failures.

Instructor notes:

- Expect students to mention retry/backoff and deduplication. Give credit for describing how to test (staging, sandbox domains).

---

## Scenario 5: Periodic Reports and Scheduled Tasks

Estimated time: 10 minutes
Learning objectives:

- Select simple schedulers versus DAG orchestrators.
- Design for monitoring and retries.

Suggested answers / key points:

- For simple nightly jobs use cron, Windows Task Scheduler, or Kubernetes CronJob.
- For workflows with dependencies or complex transformations use Airflow/Prefect; otherwise, keep it simple.
- Add logging, job status tracking, alerts on failure, and retries with dead-letter handling.

Instructor notes:

- Discuss idempotency and side effects; point out pitfalls when writing long-running tasks without proper timeouts.

---

## Scenario 6: Authentication for Internal Users

Estimated time: 10–15 minutes
Learning objectives:

- Integrate applications with existing identity systems.
- Understand SSO and role mapping basics.

Suggested answers / key points:

- Integrate with Active Directory directly (LDAP) for simple internal apps, or use an identity broker (Keycloak, Azure AD) to add modern protocols (OIDC/OAuth2) and better token handling.
- SSO: leverage Kerberos/NTLM (legacy) or OIDC via Azure AD for web SSO. Map AD groups to app roles.
- Secure tokens and rotate credentials via a secrets manager; use HTTPS, limit token lifetimes, and use refresh tokens safely.

Instructor notes:

- Give extra points for mentioning centralized logging for auth failures and multi-factor where required.

---

## Scenario 7: Speeding Up Slow Reads with Caching

Estimated time: 10 minutes
Learning objectives:

- Pick an appropriate caching strategy and store for small deployments.
- Discuss cache invalidation trade-offs.

Suggested answers / key points:

- Try cache-aside (application checks cache, falls back to DB) first—simple and common.
- Use Redis for small deployments; Memcached if only simple KV caching needed.
- For invalidation: TTLs for simple freshness, explicit invalidation on writes for stronger consistency; consider eventual consistency trade-offs.

Instructor notes:

- Ask students to estimate cache hit ratio effects and when caching adds complexity instead of benefit.

---

## Scenario 8: Simple Logging and Monitoring for an Internal App

Estimated time: 10 minutes
Learning objectives:

- Implement a minimal observability pipeline.
- Choose useful metrics and alerts for a small team.

Suggested answers / key points:

- Minimal stack: structured logs shipped to a central store (ELK, Loki, or even a shared log file rotated and emailed), and Prometheus-style metrics scraped to a Grafana dashboard.
- Instrument request latency, error rate, CPU/memory, and key business counters.
- Alerts: high error rate threshold, service unreachable, and high latency percentiles (p95/p99).

Instructor notes:

- Encourage incremental observability: start small, add alerts for actionable conditions.

---

## Scenario 9: Backup and Recovery for a Small Database

Estimated time: 10 minutes
Learning objectives:

- Define simple backup schedules and recovery testing.
- Understand offsite storage importance.

Suggested answers / key points:

- Reasonable schedule: nightly full backups plus more frequent transaction log/backups depending on RPO/RTO needs.
- Retention: keep recent backups for quick restores and older backups for compliance (policy-dependent).
- Test restores periodically to validate backups.
- Offsite/cloud storage recommended for disaster protection.

Instructor notes:

- Ask students to suggest RTO/RPO numbers and justify them; give credit for concrete restore testing steps.

---

## Scenario 10: Integrating with a Third-Party API

Estimated time: 10–15 minutes
Learning objectives:

- Build robust integration strategies for unreliable external services.
- Handle rate limits and credential management.

Suggested answers / key points:

- Use retries with exponential backoff and jitter for transient failures; implement circuit breakers to avoid overload.
- Queue outgoing requests when immediate delivery is not required; reconcile with callbacks/webhooks for async flows.
- Respect rate limits with client-side throttling and backoff; cache responses when appropriate.
- Secure keys in a secrets manager and rotate them regularly.
- Monitor integration errors and alert on sustained failures.

Instructor notes:

- Probe for idempotency and data reconciliation approaches (how to detect duplicate deliveries or missed updates).

---

## Quick grading rubric (suggested, out of 10)

- Identification of primary quality attributes: 2 points
- Correct, simple architecture choice for the scope: 3 points
- Operational considerations (monitoring, backups, security): 3 points
- Awareness of trade-offs and tests (restore tests, pilot updates, canary): 2 points

---

Last updated: 2025-10-31
