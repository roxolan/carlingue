# Architecture and ownership

This describes the intended v1 architecture. Only documentation exists today.
The deployment decision is recorded in
[ADR 0001](adr/0001-aws-fargate-and-platform-boundary.md).

## Repositories and consumers

| Repository | Responsibility |
| --- | --- |
| Carlingue | Platform packages, shared infrastructure, CI generator, templates, and the minimal in-tree sample |
| [Guichet](https://github.com/roxolan/guichet) | Public lending-intake tenant, including domain types, rules, prompts, synthetic generator, services, and evaluations |
| Corbeille, v2 | Private paper-trading tenant; work starts only after the v1 finish criteria |

The planned flow is a repository change, Buildkite validation, a versioned image,
and a Pulumi-managed environment. PR environments are seeded and smoke-tested;
demo deployments use the same path with an explicit deployment gate. A scheduled
reaper removes environments on PR closure, expiry, or orphan detection.

## Infrastructure ownership

| Owner | Planned paths | Owns |
| --- | --- | --- |
| Terraform base layer | `infra/terraform/live/base/` | Shared network, state storage, and DNS foundation |
| Terraform platform layer | `infra/terraform/live/platform/` | Shared ECS cluster, ECR registry resources, ALB/listener and TLS certificates, platform IAM, CI agents, and observability infrastructure |
| Platform output bridge | `infra/pulumi/platform/` | Publishes versioned references to shared resources as the `carlingue-platform` stack interface |
| Pulumi tenant component | `packages/platform-service/`, called from tenant `infra/` | Per-environment task definitions, services, task/execution roles, service security groups, ingress rules/target groups, and requested data resources |
| Buildkite | `.buildkite/`, `packages/pipeline-generator/` | Runs validation, build, migration, seeding, deployment, smoke, evaluation, and teardown commands |
| Tenant repository | Its own services and packages | Application behavior, schemas, migrations, prompts, and evaluation expectations |

A resource has one lifecycle owner. The output bridge exposes Terraform-owned
resource identifiers without taking ownership of those resources. Only the
platform may read the controlled Terraform outputs needed for this bridge.
Destroying a tenant environment must preserve shared resources and other tenants.

## Tenant interface

A tenant may depend on three platform surfaces:

1. Published `@carlingue/*` packages at exact registry versions.
2. Versioned `carlingue-platform` outputs through a Pulumi `StackReference`.
3. The versioned `carlingue/base` container image.

`AppSpec` declares application intent. `PlatformOutputs` describes the Fargate
resources a deployment can use, such as network, cluster, registry, ingress,
secret-store references, and telemetry endpoints. Neither carries credentials.
Their initial definitions belong in `packages/app-spec/`; provisioning follows later.

Committed tenant code must not import platform source files, copy platform
implementation, or read Terraform state. `workspace:` is valid within each
repository; it is not valid for a tenant's external `@carlingue/*` dependencies.
Optional local linking
must not change committed manifests or lockfiles. In-tree sample dependencies
may use workspace packages; external package consumption is checked separately.

## Development, delivery, and limits

Development starts with Docker Compose. M0 exercises the sample's real needs;
the backing services for Guichet and Langfuse are added as consumers arrive.
AWS authentication is first required for the M3 infrastructure bootstrap.

Buildkite remains the CI and deployment orchestrator. M0 uses a temporary
self-hosted agent for trusted work until M3 provisions the fleet. Untrusted code
must not gain credentials or privileged-agent access by selecting a queue in
repository-controlled YAML. Fork jobs require a separately isolated execution
environment; they must not run on the personal bootstrap agent.

The proposed npm publishing adapter is a narrow GitHub-hosted Actions workflow
that verifies Buildkite success for the same source commit. Record and validate
that release design before enabling it. No publisher or package exists yet.

The initial AWS direction is one region, a shared ALB, and public-subnet Fargate
tasks with restricted ingress and no NAT gateway. The region, domain, durable
data-store placement, queue technology, and state backend remain to be finalized
before provisioning. Preview hostname design should use one wildcard-compatible
level, such as `ui-guichet-pr-42.preview.<domain>`; record the final convention
before implementing ingress. Fargate selection alone does not settle persistence.

Multi-region infrastructure, production customer workloads, real lending data,
underwriting, and the private tenant's implementation are outside v1 scope.
