# Milestones and the M0 task

**Current state:** initial documentation only. M0 is in progress; no packages,
toolchain, CI pipeline, published release, or cloud environment exists yet.

## M0: scaffolding and release mechanism

**Intent:** make Carlingue usable as a development and delivery framework, then
prove that an independent Guichet checkout can consume its published packages.
AWS provisioning is outside this milestone.

| Sequence | Concern and expected paths | Acceptance |
| --- | --- | --- |
| 1, this change | `README.md`, `AGENTS.md`, `CONTRIBUTING.md`, `.github/pull_request_template.md`, `docs/` | Scope, Fargate choice, ownership, task, and contribution guidance are consistent and linked |
| 2 | Root workspace manifests, lockfile, TypeScript/lint/test configuration | Pinned tools and reproducible installation; documented root checks work |
| 3 | `packages/app-spec/` | AppSpec and PlatformOutputs types plus runtime validation; invalid contracts fail usefully |
| 4 | `examples/hello-tenant/`, container recipe, `docker-compose.yml` | Sample builds, starts locally, and passes health/smoke checks |
| 5 | `packages/pipeline-generator/` | Generates checks for changed packages and dependents; supports local pipeline preview |
| 6 | `.changeset/`, package manifests, release scripts/checks | Stable and snapshot artifacts have correct exports, files, and dependency versions; packed-consumer checks pass |
| 7 | `templates/create-app/`, `templates/create-tenant/`, scaffold scripts | Generated projects build, lint, and test outside Carlingue; local linking is reversible |
| 8 | `.buildkite/`, bootstrap instructions | A real Buildkite validation run succeeds on a temporary trusted agent; fork isolation is enforced |
| 9 | Release workflow and documentation | First real npm snapshot publishes and installs in a clean external checkout |
| 10 | Guichet repository, through its own changes | Existing repository is reconciled with the tenant template and pins published platform versions |
| 11 | Release metadata, Guichet dependency bump, evidence links | Stable release and snapshot-to-stable upgrade succeed; M0 results are recorded |

Each concern should be independently reviewable. The maintainer makes commits
in each repository. Both repositories already exist: template generation must
preserve and reconcile their documentation rather than recreate or overwrite them.

### Exit checklist

- [x] Initial architecture, Fargate ADR, task, ADR/PR templates, and working agreements.
- [ ] Monorepo tooling and frozen-lockfile installation work from a fresh checkout.
- [ ] AppSpec validation and PlatformOutputs are implemented and tested.
- [ ] The sample runs in Compose and its smoke check passes.
- [ ] Both scaffold commands produce projects that build, lint, and test.
- [ ] Affected-package CI includes dependent packages and handles root changes.
- [ ] The npm scope is available to the maintainer; publication identity is configured.
- [ ] Stable and `main` snapshot release paths are live, with immutable versions.
- [ ] Guichet consumes exact published versions and passes checks independently.
- [ ] A stable release and Guichet snapshot-to-stable bump are demonstrated.

Before the first publish, resolve namespace ownership and verify the release
toolchain's current authentication behavior. Snapshots are planned for all
publishable packages after successful `main` merges, under the `snapshot` tag;
stable versions go through a release PR. Snapshot version edits stay in a
disposable release workspace. No empty packages are published just to fill a tree.

Template checks may use packed artifacts before publication; the final consumer
check must use registry versions. Templates initially cover local execution and
CI; the deployable Pulumi program is added with PlatformService in M4.

M0 needs GitHub, Buildkite, and npm setup for its live checks. It does not need AWS
access. The temporary Buildkite agent is replaced by the managed fleet in M3.

## Later milestones and dependencies

| Milestone | Outcome | Depends on |
| --- | --- | --- |
| M1 | Guichet's seeded synthetic generator and Carlingue's generic eval runner | Usable M0 contracts and releases; independent generator work may overlap M0 |
| M2 | Guichet intake, worker, rules, review UI, audit, and local evaluation end to end | M1 fixtures/evaluations and the required published platform packages |
| M3 | AWS base infrastructure, agent fleet, observability, and published platform outputs | Stable M0 framework; can proceed alongside M1/M2 |
| M4 | Sample, then Guichet, deploy to PR/demo stacks; reaper and smoke checks work | M3 and runnable consumers; Guichet deployment also needs M2 |
| M5 | Evaluation gate blocks regressions and nightly evaluations run | M1/M2 evaluation behavior and M4 delivery path |
| M6 | Measured SLOs, alerting, backup/restore, drift detection, and incident exercise | Deployed M4 environments and M5 quality gate |
| M7 | Operating docs, boundary explanation, and evidence are complete | Documentation grows throughout; completion follows the operating exercises |

## v1 finish criteria

Before Corbeille or other v2 work begins, demonstrate PR/demo deployment through
the same pipeline; PR seeding, smoke, and teardown on closure/TTL; a blocked eval
merge; a Terraform plan annotation, failed apply and fix, and detected drift;
measured SLOs and a fired alert; a restore drill; and an incident with a used
runbook and postmortem. Complete the architecture/boundary/runbook documentation
and show Guichet consuming published packages with a merged dependency-bump PR.

Link actual builds and results as they occur. A planned check or generated
pipeline is not evidence that the corresponding operation has run.
