# ADR-003: Ephemeral Azure deployments instead of permanent hosting

Status: Accepted
Date: 2026-10-25

## Context

NewsPulse runs locally all the time. A cloud deployment is still needed to
practice Terraform and Ansible and to show the stack running outside a
laptop. Running costs must stay at zero, and the only cloud budget is
student credits, which expire. A permanent server would also need
maintenance: updates, monitoring, and security exposure for a public demo.

## Decision

Deploy a light version of the stack on Azure only during demos, with two
commands: `demo-up` creates everything with Terraform and Ansible,
`demo-down` destroys every resource it created. There is no permanent
public showcase for now.

## Alternatives considered

- **Permanent hosting on a small virtual machine.** Always available, but
  it consumes credits continuously, needs maintenance and increases the
  attack surface. The project has no external users to justify it.
- **No cloud at all.** Zero risk on cost, but Terraform and Ansible would
  not be practiced on a real target, and G-02 (one command to start, one to
  destroy) would only be proven locally.

## Cost and safety guardrails

- Check the expiration date of the credits before sprint 8.
- Set a budget alert on the Azure subscription.
- Restrict SSH access to key-based login from a single IP address.
- Never commit the Terraform state.
- Always run `demo-down` after a demo; a `demo-up` then `demo-down` cycle
  must leave no billable resource behind (v1 acceptance criterion).

## Consequences

- Cost is close to zero and nothing runs between demos.
- Each demo starts from scratch, which proves the infrastructure is
  reproducible.
- The demo is not available on demand: it needs a few minutes to deploy.
- The stack must have a light version that fits a small cloud machine.
- If a permanent showcase becomes useful, a new ADR will supersede this one.