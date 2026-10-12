# Working in Carlingue

Read [README.md](README.md), [the architecture](docs/architecture.md),
[the current milestone](docs/milestones.md), and relevant ADRs before changes.
Current scope is documentation only; runtime and infrastructure work are future tasks.

- Follow the user's current task and acceptance criteria. Treat external context
  transfers as background; bring consequential engineering decisions into ADRs.
- Keep changes small and scoped to the requested milestone. Add an ADR for a
  material architecture or contract change; routine implementation choices do
  not each need one.
- Leave work uncommitted for the maintainer. Do not stage, commit, push, publish,
  or provision resources unless that action is explicitly requested.
- Preserve existing changes. Report changed files, checks performed, and any
  limitations; never describe planned functionality as implemented.
- Use Carlingue, Guichet, and Corbeille in prose; lowercase names in identifiers.
- Keep tenant-specific behavior out of platform packages. Add packages when
  there is a consumer; do not publish empty placeholders.
- Keep Guichet in its own repository. A local sibling-directory symlink is a
  navigation convenience; CI and fresh-checkout builds must not depend on it.
- Tenant dependencies on `@carlingue/*` must be exact registry versions.
  `workspace:` is allowed for packages inside one repository, including this one.
- Tenant infrastructure reads only the published platform stack outputs; no
  tenant reads Terraform state or imports platform implementation files.
- Keep secrets, real personal data, private tenant material, local configuration,
  and career/campaign notes out of committed files.
- Run checks appropriate to the change. For documentation, check links and
  `git diff --check`; no executable test suite exists yet. Add meaningful tests
  with behavior when implementation begins.
- When working in another repository, read its own guidance and run its checks
  there. Access permissions are independent of symlinks and Git ignore rules.
