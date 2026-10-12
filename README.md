# Carlingue

Carlingue is a platform for building, testing, deploying, and operating agentic
applications. Applications are its tenants: they declare what they need through
a versioned contract and consume the platform through published artifacts.

**Status:** documentation scaffold only. The workspace tooling, packages, sample
service, CI pipelines, and cloud infrastructure have not been implemented.
There are no installation or application commands to run yet.

## Planned v1

- A TypeScript monorepo using pnpm and Turborepo.
- An `AppSpec` contract, shared packages, and service/tenant templates.
- Buildkite validation and release orchestration, with stable and snapshot npm releases.
- Local Docker Compose development and AWS Fargate deployment in a personal account.
- Terraform for shared infrastructure; Pulumi for tenant and environment stacks.
- Per-PR environments with seed data, smoke checks, expiry, and teardown.
- Evaluation gates, telemetry, SLOs, and recorded operational exercises.

The first consumers are a minimal in-tree `examples/hello-tenant` and
[Guichet](https://github.com/roxolan/guichet), a separate public repository for a
synthetic lending-intake workflow. Guichet performs document completeness and
consistency checks; it does not score applicants or make credit decisions.
Corbeille, a private paper-trading tenant, is deferred until v1 is complete.

Carlingue owns reusable platform behavior. Tenant domain logic, prompts, rules,
and synthetic lending data belong in tenant repositories. This is an engineering
demonstration using synthetic data, with no real customer workload.

## Start here

- [Architecture and ownership](docs/architecture.md)
- [M0 task, acceptance criteria, and milestone dependencies](docs/milestones.md)
- [ADR 0001: AWS Fargate and the infrastructure boundary](docs/adr/0001-aws-fargate-and-platform-boundary.md)
- [Contributing](CONTRIBUTING.md) and [agent guidance](AGENTS.md)
- [ADR template](docs/adr/template.md)

M0 establishes the development and release framework. AWS account access first
becomes necessary for infrastructure bootstrap in M3. Buildkite and npm setup
are separate M0 prerequisites for live CI and publishing.

## License

[Apache License 2.0](LICENSE).
