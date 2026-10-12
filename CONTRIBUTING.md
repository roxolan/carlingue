# Contributing

Start with the [architecture](docs/architecture.md) and
[milestone task](docs/milestones.md). This repository currently contains
documentation only; dependency installation, linting, and test commands will be
introduced with the workspace tooling.

## Changes

Work from a task that states intent, acceptance criteria, and expected paths.
Keep each change focused on one concern. Update the relevant documentation when
behavior or a contract changes. Use the [ADR template](docs/adr/template.md) for
material architecture decisions, recording alternatives and consequences.

Use conventional commit subjects such as `docs: describe platform ownership`.
The maintainer reviews and commits local changes separately in each repository;
agents leave their changes uncommitted. Prefer small PRs and squash merges.
Use the [PR template](.github/pull_request_template.md) to explain the result and
provide verification evidence. Explain the purpose of each added dependency.

For automatic local commit signing, follow
[GitHub's signing guide](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits).
Keep signing keys and machine-specific Git configuration outside the repository.
After pushing a signed commit, check its **Verified** badge on GitHub to confirm
that GitHub accepted the signature.

## Boundaries

Carlingue publishes generic platform packages. Guichet owns lending-domain code.
Internal workspace dependencies are allowed; external tenants pin published
`@carlingue/*` versions. A platform change is published before a tenant commits
its dependency bump. Local linking must leave manifests and lockfiles unchanged.

Do not commit credentials, real personal data, private tenant content, or
machine-specific configuration. Synthetic fixtures must contain invented data.
Keep personal planning and career context outside these public repositories.

## Verification

For this documentation scaffold, inspect relative links and run:

```sh
git diff --check
git status --short
```

Later changes must run the relevant package, contract, integration, or smoke
checks and report the commands and results. Database migrations must describe
their expand/contract sequence and compatibility with the running version.
Changes to published interfaces need a versioning and consumer-upgrade plan.

Publishing and cloud provisioning are separate operations from editing files.
Prepare reviewable changes before performing an explicitly requested release or
deployment. Fork-originated CI must remain isolated from privileged credentials
and the maintainer's bootstrap machine.
