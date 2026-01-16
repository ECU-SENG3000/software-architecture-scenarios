# In-class Exercises — Introductory Software Architecture

These short, instructor-friendly group exercises are designed to match the scenarios in `scenarios-introductory.md`. Each exercise is timeboxed for 15–20 minutes and includes a clear task, expected deliverable, and a compact grading rubric.

---

## How to run these exercises (recommended)

- Split the class into small groups (3–5 students).
- Give each group one scenario and the timebox shown below. Students prepare a one-paragraph architecture decision and a 3-bullet operational checklist.
- After the timebox, run a 3–5 minute group report: 60–90 seconds per group summary + 2–3 minutes for instructor feedback.
- Use the rubric below to grade quickly (10 points per group deliverable).

---

## Exercise template (15–20 minute timebox)

- Timebox: 15 minutes planning + 5 minutes report (20 minutes total) OR 12 minutes planning + 3 minutes report for a tighter class.
- Deliverable per group:
  - One short architecture decision (3–6 sentences) stating the chosen pattern and why.
  - Three operational bullets (deploy/updates, monitoring, backups/retries) — pragmatic, actionable items.

### Quick rubric (10 points)

- Quality attributes alignment: 2 points (did they identify 1–2 key quality attributes?)
- Architecture choice fit: 3 points (did they choose a simple, appropriate pattern for scope?)
- Operational checklist: 3 points (deploy/updates, monitoring, backup/retry present and reasonable?)
- Trade-offs/tests: 2 points (did they note a risk and a simple mitigation/test?)

---

## Exercise A — Desktop App update strategy (Scenario 1)

Timebox: 15–20 minutes
Task: The company must make an internal desktop app available to all employees and keep it updated without much user friction. Choose an approach and produce the deliverable.
Hints: Consider web vs native, Group Policy/SCCM/Intune, auto-updaters, security (code signing), pilot rollout and rollback.

---

## Exercise B — Intranet HR app (Scenario 2)

Timebox: 15 minutes
Task: Design a minimal, secure architecture for an HR web app accessible only on the company network. Include an authentication approach and a backup policy.

---

## Exercise C — CRUD Inventory app (Scenario 3)

Timebox: 15 minutes
Task: For a low-traffic inventory app, propose a simple deployment and DB choice. Describe the CI/CD approach and a rollback plan.

---

## Exercise D — Reliable Email Delivery (Scenario 4)

Timebox: 15 minutes
Task: Design how the app will deliver transactional emails reliably. Include whether it’s synchronous or asynchronous, retry behavior, and monitoring strategy.

---

## Exercise E — Scheduling and Reports (Scenario 5)

Timebox: 15 minutes
Task: Choose a scheduling approach for nightly reports. If dependencies grow later, how would the architecture evolve? Provide monitoring and retry guidance.

---

## Exercise F — Authentication & SSO (Scenario 6)

Timebox: 15–20 minutes
Task: The company has Active Directory. Propose an authentication approach for a new internal web app and a plan to map AD groups to app roles. Include token/storage security measures.

---

## Exercise G — Caching for Slow Reads (Scenario 7)

Timebox: 15 minutes
Task: Propose a caching strategy (store, pattern) for a report page with slow DB reads. Explain invalidation and consistency trade-offs in 2–3 short bullets.

---

## Exercise H — Observability for Small Teams (Scenario 8)

Timebox: 15 minutes
Task: Pick a minimal observability stack and define 5 metrics/logs you would collect for quick troubleshooting plus 2 alert rules.

---

## Exercise I — Backups and Recovery (Scenario 9)

Timebox: 15 minutes
Task: Define backups (frequency, retention), an RTO/RPO for a small business, and a simple verify-restore test plan.

---

## Exercise J — Third-Party API Integration (Scenario 10)

Timebox: 15–20 minutes
Task: Design a resilient integration to a rate-limited external REST API. Include queuing/retry logic, rate limiting approach, and key observability signals.

---

## Instructor shortcuts and variations

- Two shorter rounds: run four exercises in parallel for 12 minutes each then rotate scenarios.
- Peer review: after each group report, have a neighboring group provide one suggestion or a risk the presenting group missed.
- Homework: groups expand their one-paragraph decision into a 500-word short justification for submission.

Last updated: 2025-10-31
