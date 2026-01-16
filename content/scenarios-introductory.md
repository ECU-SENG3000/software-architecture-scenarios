# Introductory Software Architecture Scenarios

These short, beginner-friendly scenarios are designed for students new to software architecture. Each scenario includes a simple context, a few quality attributes to prioritize, and discussion prompts that guide students toward practical architectural thinking.

---

## Scenario 1: Internal Desktop Application — keeping clients up to date

Context: A company needs a small application used by employees on company desktops. The application must be available on all desktops and the company is worried about keeping it updated across many machines.

Quality attributes to prioritize:

- Deployability
- Operability (updates)
- Security
- Usability

Discussion prompts:

- What deployment approaches could solve the update problem (web-based app, auto-update installers, MSI/Group Policy, package managers, ClickOnce, or remote app)?
- How would you minimize user friction during updates? Consider silent updates, version compatibility, and rollback.
- What are the security and network implications for each approach (firewall rules, permissions, code signing)?
- How would you support offline use or slow networks?

---

## Scenario 2: Single-server Intranet App for HR

Context: The HR team wants a small web app accessible only from inside the company network. It will store employee records and simple workflows.

Quality attributes to prioritize:

- Simplicity
- Maintainability
- Security (internal access only)

Discussion prompts:

- What is a minimal, safe architecture to host an intranet app (single VM + RDBMS, shared hosting, or PaaS)?
- How would you secure access (network restrictions, VPN, Active Directory/LDAP)?
- What backup and restore strategy is reasonable for this scope?

---

## Scenario 3: Simple CRUD Inventory Web App

Context: A small business needs an internal app to manage product inventory (create/read/update/delete). They expect low concurrent users.

Quality attributes to prioritize:

- Correctness
- Simplicity
- Cost-effectiveness

Discussion prompts:

- Would you choose a monolith or microservices for this? Why?
- What database type is appropriate (relational vs. document vs. key-value)?
- How would you structure deployment and updates to keep operations simple?

---

## Scenario 4: Need to Send Emails Reliably

Context: The app must send transactional emails (password resets, notifications). Emails must be reliably delivered and retries handled for transient failures.

Quality attributes to prioritize:

- Reliability
- Observability
- Resilience

Discussion prompts:

- Should email sending be synchronous or delegated to a background job? Why?
- How do you design retries and idempotency for email delivery?
- What external services or SMTP strategies would you consider (third-party SMTP, transactional services like SendGrid or SES)?

---

## Scenario 5: Periodic Reports and Scheduled Tasks

Context: The company needs nightly reports and periodic data exports. Tasks must run reliably on schedule.

Quality attributes to prioritize:

- Reliability
- Observability
- Scheduling accuracy

Discussion prompts:

- What scheduling options exist on premises and in the cloud (cron, Windows Task Scheduler, Kubernetes CronJob, managed schedulers)?
- When is a simple scheduler enough versus a DAG-based orchestrator (Airflow, Prefect)?
- How would you monitor and alert on failed jobs?

---

## Scenario 6: Authentication for Internal Users

Context: An internal app needs user authentication and role-based access. The company already has Active Directory.

Quality attributes to prioritize:

- Security
- Usability
- Integration

Discussion prompts:

- What are the pros and cons of integrating directly with Active Directory vs. using an identity broker (Keycloak, Azure AD)?
- How would you implement single sign-on (SSO) and role mapping?
- How do you secure tokens/credentials and rotate them safely?

---

## Scenario 7: Speeding Up Slow Reads with Caching

Context: A report page reads data that doesn't change often, but database queries are slow and cause timeouts.

Quality attributes to prioritize:

- Performance
- Consistency (eventual vs strong)
- Simplicity

Discussion prompts:

- Which caching strategy would you try first (cache-aside, read-through, response caching) and why?
- Which cache store would you use for an on-prem or small cloud deployment (Redis, Memcached)?
- How would you handle cache invalidation when data changes?

---

## Scenario 8: Simple Logging and Monitoring for an Internal App

Context: The team wants visibility into errors and basic performance metrics for a small web app.

Quality attributes to prioritize:

- Observability
- Debuggability
- Cost-effectiveness

Discussion prompts:

- What minimal logging and metrics stack could you set up quickly (central log file aggregation, basic metrics + dashboard)?
- How would you instrument the app for request latency and error counts?
- What alert rules are useful for small teams (high error rate, service down)?

---

## Scenario 9: Backup and Recovery for a Small Database

Context: The application stores critical business data in a single database. The company needs a simple backup and recovery plan.

Quality attributes to prioritize:

- Durability
- Recoverability
- Cost

Discussion prompts:

- What backup frequency and retention policy makes sense for a small business?
- How do you test restores and verify backups are usable?
- Should backups be stored offsite or in the cloud for disaster recovery?

---

## Scenario 10: Integrating with a Third-Party API

Context: The app needs to send orders to a third-party billing service that provides a REST API. The third party has rate limits and occasional outages.

Quality attributes to prioritize:

- Resilience
- Security
- Observability

Discussion prompts:

- How would you design the integration to tolerate outages (retries with backoff, queueing, circuit breaker)?
- Should you poll or support webhooks from the third party? What are trade-offs?
- How do you secure API keys and rotate them safely?

---

Last updated: 2025-10-31

