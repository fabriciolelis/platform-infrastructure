# Platform Infrastructure

Terraform-managed infrastructure for the Formula Insights Platform.

## Purpose

This repository defines the cloud foundations and shared infrastructure required by Formula Insights. It makes infrastructure changes reviewable, reproducible, and auditable.

No cloud resource may be created or changed manually when an equivalent Terraform change is possible.

## Scope

- Terraform remote state and locking.
- AWS account baseline, tagging, and budget controls.
- Networking and required security boundaries.
- Managed Kubernetes and supporting cloud resources, when approved.
- Shared services required by the platform.

## Out of scope

- Application source code and image builds.
- Kubernetes workload versions.
- Runtime secrets and their plaintext values.

## Intended structure

```text
modules/              # reusable Terraform modules
environments/
  dev/                # development environment root module
  prod/               # production environment root module
policies/             # optional policy-as-code checks
docs/                 # provider setup and teardown notes
```

## Workflow

1. Make a small, reviewed Terraform change.
2. Run format, validation, linting, and security checks.
3. Review the Terraform plan before applying it.
4. Apply only through the approved pipeline or documented local break-glass procedure.
5. Record significant architecture changes in `platform-docs/adr`.

## Cost control

Cloud provisioning starts only after a monthly budget ceiling, cost alert, resource-tagging convention, and teardown runbook are documented. Local development uses Kind or k3d until then.

Every resource must include, where supported:

```text
project = formula-insights
environment = dev | prod
managed-by = terraform
owner = fabricio
```

## Quality gates

Every merge request must pass:

- `terraform fmt -check`;
- `terraform validate`;
- a plan for affected environments;
- infrastructure security scanning;
- review of any destructive or cost-increasing change.

## Related documents

See ADR-004 in `platform-docs` for the local-first and cost-control decision. Deployment state belongs in `platform-gitops`, not in this repository.
