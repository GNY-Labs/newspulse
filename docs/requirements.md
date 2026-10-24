# NewsPulse — Requirements Specification

Status: draft · Version: 0.1 · Scope: v1 (sprints 0 to 8)

## 1. Context

Technology watch is scattered across blogs, Hacker News and GitHub releases. NewsPulse is a personal tech radar: it collects these sources continuously, detects trending topics and shows everything in one place.

NewsPulse is also a learning project. Each sprint delivers something that runs and repositories that can be reused in other projects.

## 2. Goals

| ID | Goal |
| --- | --- |
| G-01 | One place to see trending topics, new releases of followed projects and the health of the collectors. |
| G-02 | Reproducible infrastructure: one command to start the stack, one command to destroy it. |
| G-03 | Reusable service repositories that follow a common contract. |

## 3. Scope

**In scope for v1:** collectors (Hacker News, RSS/Atom feeds, GitHub releases), ingestion API, Kafka, Flink trend jobs, PostgreSQL, observability (Prometheus, Grafana, Loki, Alloy), a first orchestrator and an ephemeral demo on Azure.

**Out of scope for v1:**

| Item | Planned for |
| --- | --- |
| Reading interface (article list, filters, detail page) | v2 |
| Topic classifier and categories (the `category` field exists but stays empty) | v2 |
| Translation and agent | v3 |
| Non-tech content categories | v4 (optional) |
| Kubernetes | Separate track, after v2 |
| Multiple users, authentication, permanent public hosting | Not planned |
| Storage of full article text | Never |

## 4. Users

A single user, the project owner, who is both the **reader** (consults dashboards) and the **operator** (starts, monitors and destroys the stack). There are no external users in v1.

## 5. Functional requirements

Each requirement is a short, testable statement. The last column gives the sprint that delivers it.

| ID | Requirement | Sprint |
| --- | --- | --- |
| FR-01 | The ingestion API stores an article sent to `POST /articles`. | 3 |
| FR-02 | Each stored article has a title, URL, source, publication date, collection date, optional excerpt and a `category` field (empty in v1). | 3 |
| FR-03 | The system never stores the same article twice (deduplication by URL). | 3 |
| FR-04 | The system collects new Hacker News stories at least every hour. | 4 |
| FR-05 | The system collects new entries from a configurable list of RSS/Atom feeds at least every hour. | 4 |
| FR-06 | The system collects new releases from a configurable list of GitHub repositories at least once a day. | 4 |
| FR-07 | A dashboard shows collector health per source: last successful run, number of articles collected and errors. | 4 |
| FR-08 | A dashboard lists the new releases of followed projects. | 4 |
| FR-09 | Collected articles go through Kafka before being written to PostgreSQL. | 5 |
| FR-10 | The system computes trending topics over two sliding windows: the last day and the last week. | 6 |
| FR-11 | A dashboard shows trending topics, updated continuously. | 6 |
| FR-12 | Enabling or disabling a service in one configuration file is enough to include or exclude it from the stack. | 7 |
| FR-13 | One command starts the whole local stack and one command stops it. | 7 |
| FR-14 | `demo-up` deploys a light version of the stack on Azure and `demo-down` destroys every resource it created. | 8 |
| FR-15 | An alert is raised when a collector misses three consecutive scheduled runs. | 4 |

## 6. Non-functional requirements

| ID | Category | Requirement | Sprint |
| --- | --- | --- | --- |
| NFR-01 | Cost | Running costs are zero: local engine free for personal use, cloud resources only during demos, paid with student credits, with a budget alert. | 0, 8 |
| NFR-02 | Resources | The full local stack runs on a single laptop within 8 GB of memory allocated to containers. | 1 to 8 |
| NFR-03 | Security | No secret is ever committed: only `.env.example` files, secret scanning and push protection enabled, Terraform state kept out of Git. | 0 to 8 |
| NFR-04 | Observability | Every service exposes metrics to Prometheus, sends its logs to Loki and ships its own dashboard. | 2 to 8 |
| NFR-05 | Content rights | Only titles, excerpts of at most 300 characters (cut at a word boundary) and links are stored; no full article text in the database or the repositories. | 3 to 8 |
| NFR-06 | Source compliance | Each source's terms of use and rate limits are checked before integration, documented, and respected by the collector. | 4 |
| NFR-07 | Reproducibility | Each service starts alone with `make up` from a fresh clone, after copying `.env.example` to `.env`. | 1 to 8 |
| NFR-08 | Quality | Every change is merged through a pull request with a green CI (YAML lint, gitleaks, hadolint, shellcheck). | 0 to 8 |
| NFR-09 | Documentation | Each service has an up-to-date `README.md` and `RUNBOOK.md`; important decisions are recorded as ADRs. All public content is in English. | 0 to 8 |

## 7. Constraints

- Local runtime: OrbStack with Docker Compose. Kubernetes comes later in a dedicated track.
- Cloud: Azure, ephemeral deployments only, with Terraform and Ansible.
- Repositories: public, MIT license, hosted in the GNY Labs GitHub organization.
- Team: one developer working part-time, in one-week sprints.

## 8. Acceptance criteria for v1

- Every FR and NFR above is verified, in the sprint indicated.
- The trends dashboard is fed by real articles collected over at least seven consecutive days.
- A `demo-up` then `demo-down` cycle on Azure leaves no billable resource behind.

## 9. Risks

| Risk | Mitigation |
| --- | --- |
| More time spent on tooling than on the technologies to learn | Orchestrator only at sprint 7; extract what repeats instead of designing it upfront |
| Stack too heavy for a laptop | Memory cap, light versions, start services one by one |
| Unexpected cloud costs | Ephemeral deployments, budget alert, systematic `terraform destroy` |
| Secret leak in public repositories | `.env.example` only, secret scanning, Terraform state out of Git |
| Third-party content rights | Titles, excerpts and links only |
| Unstable sources or restrictive terms | Check terms and rate limits before each new source |
| Lack of time | Short, verifiable exit criteria; a usable deliverable at the end of each sprint |
