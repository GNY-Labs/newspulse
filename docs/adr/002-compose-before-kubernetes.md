# ADR-002: Docker Compose before Kubernetes

Status: Accepted
Date: 2026-10-25

## Context

NewsPulse is a learning project that practices many technologies (Docker,
Terraform, Ansible, CI/CD, Kafka, Flink, MLflow, observability). Kubernetes
is one of the goals, but it adds a lot of concepts (manifests, Helm,
networking, cluster operations) on top of services that are still being
built. The full stack must also run on a laptop within 8 GB of memory.

## Decision

Run the stack locally with Docker Compose on OrbStack for v1 and v2. Learn
Kubernetes in a dedicated track after v2, using the Kubernetes built into
OrbStack, then a managed cloud cluster in an ephemeral deployment.

## Alternatives considered

- **Kubernetes from the start.** Closest to production, but it would slow
  down every sprint and mix two learning curves: building the services and
  operating a cluster. The v1 would be at risk.
- **Compose only, never Kubernetes.** Simplest, but it leaves out a major
  skill the project aims to practice.

## Consequences

- Each service is first made to work alone with `make up`, which keeps
  sprints short and results verifiable.
- The `service.yaml` file of each service declares ports, dependencies,
  health and metrics, so it can later guide Kubernetes deployments.
- Kubernetes work is postponed: the first cluster experience comes after
  v2, so v1 and v2 do not depend on it.
- Services may need small adjustments when migrated (health probes,
  configuration through ConfigMaps).
