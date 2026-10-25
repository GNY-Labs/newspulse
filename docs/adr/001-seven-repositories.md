# ADR-001: Seven repositories

Status: Accepted
Date: 2026-10-25

## Context

NewsPulse is made of reusable infrastructure services (PostgreSQL, Kafka,
Flink, MLflow, observability), an orchestrator, application code,
documentation and infrastructure-as-code. The project must keep services
reusable in other projects and give practice with a multi-repository
workflow, without an administration cost that outweighs the learning.

## Decision

Use seven repositories in the GNY Labs organization:

- five reusable services: `svc-postgres`, `svc-observability`, `svc-kafka`,
  `svc-flink`, `svc-mlflow`;
- `platform`: orchestrator, service template, shared CI workflows;
- `newspulse`: applications as subfolders, plus `docs/` and `infra/`.

A folder becomes its own repository only if it is reusable on its own in
another project or has a different lifecycle.

## Alternatives considered

- **One repository per folder.** Maximum isolation, but every repository
  needs its own CI, protections, README and issues. Most folders (docs,
  infra, applications) are not reusable alone, so the cost buys nothing.
- **A single repository.** Simplest to administer, but services would not be
  reusable on their own and the multi-repository workflow would not be
  practiced.

## Consequences

- Each `svc-*` repository follows the same service contract and can be
  reused as is.
- Shared CI lives in `platform` and is called from the other repositories.
- Folders of `newspulse` can be extracted later if a real need appears.
- Versions must be aligned across repositories, which the orchestrator
  will handle from sprint 7.
