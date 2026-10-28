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
- For collaboration, use short-lived branches such as `feat/schema-repair` and pull requests with a summary, validation, and limitations. Small authorised solo changes may go directly to `main`.

## Definition of done

- The change has one clear purpose and includes relevant documentation.
- Runtime changes include meaningful checks for affected behaviour, including failure paths when relevant.
- README claims distinguish working functionality from planned work.
- No credentials, real student records, database files, generated logs, or local environment files are staged.
- Changes are committed and pushed; report the commit and any remaining limitations.

## Recovery-system invariants

- AI proposes structured patches and allowed actions; deterministic validation controls execution.
- Never invent payments, approvals, academic eligibility, or seat availability.
- Preserve source events and protect student, payment, course, and term identities.
- Recheck current prerequisites before writes; use idempotency and bounded retries.
- Forbidden changes remain blocked even with human approval.
- Mark an incident recovered only after verifying the intended destination records.
- Use fictional data and explicitly identify simulated integrations and injected failures.

## Observability

- Prometheus stores metrics, Loki stores logs, Alloy collects logs, and Grafana displays both.
- Keep high-cardinality identifiers such as `workflow_id` and `event_id` in structured log bodies, not metric or Loki labels.
- Redact credentials and personal data before logging or sending diagnostic context to an AI model.
- Pin dependency and container versions; validate configuration before pushing changes.

## Pull request notes

Describe the problem, the resulting behaviour, checks actually run, and material limitations. Link related requirements or incidents when relevant.
