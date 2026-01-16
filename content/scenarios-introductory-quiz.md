# Mini Quiz — Introductory Software Architecture (multiple choice)

This quick quiz is intended for short in-class checks or low-stakes quizzes. Each question is single-best-answer. An answer key with short explanations follows.

Time: 10–15 minutes

---

1. A company needs to keep a native Windows desktop app up to date across 500 corporate machines. Which is the simplest operationally for an enterprise Windows shop?

A. Convert the app to a cloud-native microservice
B. Use Group Policy/SCCM/Intune to push a signed MSI
C. Require every user to download installers manually
D. Use a peer-to-peer file share for updates

1. For a low-traffic CRUD app that stores structured data, which database is the most appropriate starting choice?

A. PostgreSQL (relational)
B. MongoDB (document)
C. Cassandra (wide-column)
D. Redis (in-memory key-value only)

1. When sending transactional emails (password resets), what's the recommended pattern to keep the web request responsive and make delivery reliable?

A. Send synchronously during the HTTP request
B. Delegate sending to a background worker/queue
C. Block the request until the SMTP server confirms delivery
D. Send via UDP for speed

1. Which caching pattern is simplest and safest for adding cache to an existing read-heavy app?

A. Cache-aside (application-managed)
B. Write-through cache
C. Read-through with automatic invalidation
D. Distributed write-behind with eventual consistency

1. Your nightly report job occasionally fails due to a flaky query. What is the simplest way to make the job more robust?

A. Migrate to Airflow immediately
B. Add retries with exponential backoff and alerting on repeated failures
C. Rewrite the entire database schema
D. Run the job on multiple machines in parallel without coordination

1. The company has Active Directory. For a new internal web app, which approach gives the fastest, lowest-effort SSO integration?

A. Build a custom username/password store
B. Integrate with AD/LDAP or use Azure AD with OIDC
C. Use SMS-based login for all users
D. Require users to create GitHub accounts

1. Which technique helps avoid duplicate side effects when a background job may be retried at-least-once?

A. Use idempotency keys and record delivered status
B. Don’t retry at all
C. Always accept duplicate deliveries - users won’t notice
D. Use UDP to avoid duplicate packets

1. For a small web app where you want basic observability on request latency and errors, which minimal stack is reasonable?

A. Prometheus for metrics + Grafana dashboard + structured logs shipped to a central store
B. Build a custom monitoring server from scratch
C. No instrumentation and rely on user reports
D. Only use client-side console logs

1. A third-party REST API you call is rate-limited and occasionally unavailable. Which combination is the most resilient?

A. Retries with exponential backoff + client-side rate limiting + circuit breaker + queue for async calls
B. Do nothing and hope for the best
C. Poll the third-party API constantly to keep connection alive
D. Send all requests in parallel and hope the fastest wins

1. For backups of a small production database with modest RTO/RPO needs, which is a reasonable initial policy?

A. Nightly full backups + periodic transaction log/ incremental backups, test restores monthly
B. No backups; rely on hardware RAID
C. Backups only when someone remembers to run them
D. Keep backups only on the same disk as the DB for convenience

---

## Answer key and brief explanations

1. B — Use Group Policy/SCCM/Intune to push a signed MSI

Explanation: For enterprise Windows environments this is operationally simplest and integrates with corporate management tools.

2. A — PostgreSQL (relational)

Explanation: Structured CRUD with integrity constraints is a natural fit for a relational DB.

3. B — Delegate sending to a background worker/queue

Explanation: Keeps web requests responsive and lets retries/deliverability be handled asynchronously.

4. A — Cache-aside (application-managed)

Explanation: Simple to add to existing apps and keeps cache control explicit.

5. B — Add retries with exponential backoff and alerting on repeated failures

Explanation: Easiest immediate mitigation; migrating to an orchestrator is heavier-weight and only needed for complex workflows.

6. B — Integrate with AD/LDAP or use Azure AD with OIDC

Explanation: Leverages existing identity providers and minimizes new account management.

7. A — Use idempotency keys and record delivered status

Explanation: Idempotency prevents duplicate side effects when retries happen.

8. A — Prometheus for metrics + Grafana dashboard + structured logs shipped to a central store

Explanation: Lightweight, widely used, and provides actionable metrics and logs.

9. A — Retries with exponential backoff + client-side rate limiting + circuit breaker + queue for async calls

Explanation: Combination balances resilience, rate-limit respect, and avoids overwhelming the third party.

10. A — Nightly full backups + periodic transaction log/ incremental backups, test restores monthly

Explanation: Practical and protects data; test restores validate backup usability.

Last updated: 2025-10-31
