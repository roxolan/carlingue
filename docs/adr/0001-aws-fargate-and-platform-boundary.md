# ADR 0001: AWS Fargate and the platform infrastructure boundary

- Status: Accepted
- Date: 2026-10-11
- Scope: v1 deployment architecture; implementation follows in M3/M4

## Context

Carlingue needs one deployment substrate that supports independently versioned
tenants, PR environments, and a long-lived demo. The platform should demonstrate
ECS deployment and infrastructure ownership while keeping local development
independent of cloud resources. Guichet is a separate public consumer from the start.

## Decision

Use AWS Fargate in the maintainer's personal AWS account for v1 tenant compute.
Use Docker Compose locally. Keep a single-region starting scope and one shared
ALB; the initial network direction avoids a NAT gateway and restricts task ingress.
Resolve account region, persistence, and detailed network configuration before
provisioning. This decision authorizes an architecture, not an infrastructure apply.

Terraform owns long-lived shared resources: network, state storage, DNS, ECS
cluster, registry, shared ingress, platform IAM, CI fleet, and observability
infrastructure. Pulumi owns per-tenant/per-environment resources through the
planned PlatformService component. Each resource has exactly one lifecycle owner.

The `carlingue-platform` Pulumi stack publishes versioned identifiers for shared
resources. The platform may obtain these from Terraform outputs, but the bridge
does not duplicate ownership. Tenants consume only published `@carlingue/*`
packages, these outputs through StackReference, and the versioned base image.
They do not access Terraform state or import platform implementation files.

Keep Carlingue and Guichet in separate repositories. In-repository dependencies
may use `workspace:`. A tenant's external platform dependencies must use exact
registry versions. Local linking is optional and must preserve committed manifests.

## Consequences

- The tenant contract can stay focused on application intent while its published
  infrastructure outputs describe the chosen AWS substrate.
- M0 through local M2 work requires no AWS access. M3 introduces account
  authentication and provisions the shared infrastructure.
- Shared-resource cost, cleanup, quotas, and per-environment isolation need
  explicit implementation and operating checks.
- Data-store durability and observability hosting still need designs; choosing
  Fargate does not imply every backing service runs as a disposable task.
- Guichet iteration includes publishing platform changes and updating its pins.
- The in-tree sample validates platform changes without depending on Guichet's CI.

## Alternatives considered

- ECS Anywhere with external compute: adds a cross-cloud operating boundary.
- Hetzner-only deployment: adds a different orchestration substrate.
- Hetzner/AWS hybrid: adds a second hosting environment to v1.
- One IaC tool: simpler tooling, but loses the deliberate shared-resource versus
  environment-lifecycle split selected for this project.

## Follow-up

Implement and validate AppSpec/PlatformOutputs in M0; finalize infrastructure
details in M3; implement tenant provisioning and teardown in M4. Record material
changes in a superseding ADR and update the [ownership table](../architecture.md).
