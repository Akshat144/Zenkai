# Zenkai

**Keep campus moving.**

An AI-powered recovery layer for university ERP and portal workflows. Zenkai detects broken integrations and incomplete outcomes, diagnoses the likely cause, proposes a constrained repair, validates it, and resumes the failed step with human approval where necessary.

Built for **CODE E FIESTA 2026 — Challenge 4: The Self-Healing AI Automation System**, a six-hour AI hackathon organised by Medicaps University with SlowBros Labs.

## Project status

**Repository and observability foundation.** Contribution standards, the MVP specification, and a Docker Compose observability stack with a provisioned dashboard are in place. The recovery engine, demo integrations, and student interface are planned work; this repository does not yet demonstrate end-to-end healing.

## The problem

A student pays their fees. Their adviser approves their subjects. Registration remains pending because a request failed between systems. Finance, academics, and IT each see part of the process, while the student remains stuck.

Zenkai monitors whether the intended business outcome actually happened, rather than treating an available server or a successful HTTP response as proof of completion.

The product is designed for university systems generally. It does not depend on one institution or claim an existing POD.ai or other vendor integration.

## Hackathon requirements and acceptance criteria

All capabilities below are **planned** until implemented and verified.

| Challenge requirement | Planned implementation | Evidence required in the demo |
| --- | --- | --- |
| Monitor workflows and detect errors or unexpected outcomes | Capture workflow events, request errors, and overdue incomplete outcomes | Inject a failure into a previously working registration journey; an incident appears automatically |
| Analyse logs or execution data for likely root cause | Correlate structured logs, request payloads, destination schemas, and prerequisite records | Diagnosis cites actual event/log evidence and identifies the failed step |
| Recommend or generate corrective action | AI generates a restricted field-mapping patch or selects an allowed recovery action | Display the proposed action and an exact before/after diff |
| Validate the proposed fix | Check supported operations, schema, immutable facts, business prerequisites, and a dry run | Valid repairs pass; a proposal that fabricates approval is blocked |
| Retry with human approval where necessary | Review new mappings or routing changes; replay the failed step with its original operation identity | Approval authorises the displayed action only; repeated execution does not duplicate enrolment |
| End-to-end failure injection and successful recovery | Verify destination records after replay | The student can open the enrolled course in the demo learning portal |

## Flagship MVP: a semester registration that recovers

```text
Fee clearance verified → Adviser approval recorded → Registration → Course access
                                                        ↓ failure
       Detect → Collect evidence → Diagnose → Propose patch → Validate
                                                        ↓
                           Approve when required → Replay → Verify outcome
```

1. A fictional student's fee clearance and academic approval already exist.
2. A failure control changes the demo registration API contract from `semester_id` to `term_id`.
3. The old integration sends a request that is genuinely rejected.
4. Zenkai detects the failure and the missing enrolment, then collects relevant execution evidence.
5. AI proposes a field mapping that preserves the authoritative term value.
6. Deterministic checks validate the patch and current prerequisites; a new mapping requires approval.
7. Zenkai replays registration with the same operation identity and provisions course access.
8. The student opens the course. Destination records prove completion.

## Three controlled failure scenarios

| Scenario | Recovery behaviour | Success condition |
| --- | --- | --- |
| Schema drift | Generate and validate a field-mapping patch | Correct registration and course access exist |
| Connection failure or timeout | Preserve the job, reconcile an uncertain outcome, then use bounded retries with backoff when appropriate | Recovery creates no duplicate enrolment, including when the first write succeeded but its response was lost |
| Broken departmental handoff | Detect an inactive owner or invalid queue; propose an authorised replacement from configured directory data | Approved task delivery resumes without inventing academic approval |

A separate **unsafe-proposal injection** tests the validator. Label it as injected test input, rather than a genuine live-model response. It must be rejected without changing business records.

## What AI does

AI interprets the incident's evidence and returns a structured diagnosis, supporting evidence references, a constrained patch or registered action, and expected validation results. It does not receive arbitrary shell or database execution authority.

Deterministic application code handles monitoring rules, permissions, validation, idempotency, retries, execution, and outcome verification. Model confidence alone never authorises a write.

## Integration model

Each connector declares its readable events and records, writable actions, schemas, and authorisation requirements. Workflow completion rules are configured by the university.

```text
ERP / Finance / Registration / Learning portal
                     ↕ authorised connectors
                   Zenkai
          Observe → Diagnose → Validate → Recover
                     ↕
           Student and administrator views
```

The hackathon uses fictional data and explicitly labelled demo services. Real vendor integration requires documented, approved interfaces and suitable credentials. Read-only access supports diagnosis; executing a repair requires appropriate write permissions. Vendor-internal code and physical infrastructure are outside this adapter's control.

## Observability stack

| Component | Responsibility |
| --- | --- |
| [Prometheus](https://prometheus.io/docs/introduction/overview/) | Scrape service metrics: errors, request latency, pending workflows, repair outcomes, and recovery duration |
| [Grafana](https://grafana.com/docs/grafana/latest/) | Display service health, incidents, metrics, and correlated log evidence |
| [Grafana Loki](https://grafana.com/docs/loki/latest/) | Store and query structured execution logs |
| [Grafana Alloy](https://grafana.com/docs/alloy/latest/) | Collect application logs and forward them to Loki |

Use Alloy for new log collection; [Promtail reached end of life on March 2, 2026](https://grafana.com/docs/loki/latest/send-data/promtail/).

Planned log fields include `timestamp`, `level`, `service`, `workflow_id`, `event_id`, `incident_id`, `step`, `outcome`, and `error_code`. Keep per-event identifiers in log bodies, not metric or Loki labels. Redact secrets and personal data before collection or model use.

The durable workflow database remains the source of truth; telemetry supports detection and diagnosis rather than replacing transaction records.

## Safety and completion rules

- Preserve original events; apply patches to working copies after authenticating the source.
- Restrict patches to explicitly supported mappings or safe representations; preserve semantic values.
- Never fabricate payments, academic approval, eligibility, or seat availability.
- Recheck current prerequisites immediately before writes and enforce capacity atomically at the destination.
- Reject forbidden changes even if a human attempts to approve them.
- Scope approved mappings to the connector and schema version; validate every replay.
- Use database-enforced operation uniqueness, bounded retries, and an audit trail.
- On ambiguity or exhausted retries, hold the workflow and escalate with evidence.
- Mark recovery only after destination enrolment and course-access records are verified.

## Six-hour implementation order

1. Build a normal registration journey across small finance, registration, and learning-portal demo services.
2. Add durable workflow state, structured logs, metrics, and deterministic failure controls.
3. Implement schema-drift diagnosis, runtime patch generation, validation, approval, and replay.
4. Verify timeout reconciliation, duplicate prevention, and unsafe-proposal rejection.
5. Add the constrained handoff-repair scenario, then polish the student journey and incident timeline.
6. Rehearse working failure-to-recovery flows; report measured timings and real record counts.

Full payment processing, arbitrary code repair, broad vendor integration, and bulk recovery are outside the initial MVP.

## Development and commit hygiene

Read [CONTRIBUTING.md](CONTRIBUTING.md) before changing the repository. Coding agents must also follow [AGENTS.md](AGENTS.md).

Complete one coherent change, verify it, commit it with a Conventional Commit message, and push before starting the next change. Preserve other contributors' work and never commit secrets or real student records.

```bash
git clone https://github.com/arnavsx3/Zenkai.git
cd Zenkai
```

Start the observability foundation using [observability/README.md](observability/README.md). Application run commands will be added with the implementation. Cloning the repository alone does not start a recovery service.
