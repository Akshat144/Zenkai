# Contributing to Zenkai

Zenkai is a six-hour hackathon project. Keep changes small, reviewable, and runnable.

## Commit and push cadence

- Finish one coherent change, verify it, commit it, and push it before starting the next change. Do not create a commit for every keystroke or intermediate broken edit.
- Use Conventional Commits: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, or `chore:` followed by a short imperative description.
- Keep unrelated work in separate commits. Explain non-obvious decisions and validation in the commit body.
- Before staging, inspect `git status --short` and `git diff`. Stage explicit file paths, then inspect `git diff --cached` and run `git diff --cached --check`.
- Run checks appropriate to the change. Record failures or unavailable checks honestly; never describe an unrun check as passing.
- Push each verified commit with `git push`. For a new branch, use `git push -u origin <branch>`.
- If a push fails, fix the authentication or divergence issue before continuing. Never silently leave completed changes unpushed.
- Preserve other contributors' work. Do not force-push shared branches, rewrite published history, or discard unrelated changes. Prefer a corrective commit or `git revert`.
- For collaboration, use short-lived branches such as `feat/service-recovery` and pull requests with a summary, validation, and limitations. Small authorised solo changes may go directly to `main`.

## Definition of done

- The change has one clear purpose and includes relevant documentation.
- Runtime changes include meaningful checks for affected behaviour, including failure paths when relevant.
- README claims distinguish working functionality from planned work.
- No credentials, real student records, database files, generated logs, or local environment files are staged.
- Changes are committed and pushed; report the commit and any remaining limitations.

## Recovery-system invariants

- AI proposes structured configuration patches and registered operational actions; deterministic validation controls execution.
- Preserve original evidence and known-good configuration snapshots. Never log credentials or expose arbitrary shell/database execution to the model.
- Restrict actions to authorised targets and check configuration revisions, permissions, resource budgets, and current service state before writes.
- Bind human approval to the exact proposal and target. Forbidden changes remain blocked even with approval.
- Use action identities, per-target execution locks, cooldowns, and bounded retries; stop and escalate after failed verification.
- Verify service health and correct requests under sustained test traffic before marking an incident recovered.
- Keep student records, payments, and academic decisions outside infrastructure-repair actions.
- Explicitly label demo services, injected faults, and injected unsafe proposals.

## Observability

- Prometheus stores metrics, Loki stores logs, Alloy collects logs, and Grafana displays both.
- Keep high-cardinality identifiers such as `request_id` and `incident_id` in structured log bodies, not metric or Loki labels.
- Redact credentials and personal data before logging or sending diagnostic context to an AI model.
- Pin dependency and container versions; validate configuration before pushing changes.

## Load testing with k6

- Use bounded, repeatable tests against the owned demo environment or explicitly authorised targets.
- Define response checks and thresholds; record baseline, injected-failure, and recovery windows separately.
- Keep offered traffic stable through recovery. Report achieved throughput, latency, errors, rejections, and dropped iterations.
- A passing health probe or a stopped load generator is insufficient evidence of recovery.
- Only report load-test and healing results that were actually measured.

## Pull request notes

Describe the problem, the resulting behaviour, checks actually run, and material limitations. Link related requirements or incidents when relevant.
