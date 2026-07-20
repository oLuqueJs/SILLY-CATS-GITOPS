# ADR-0001: Use Argo CD App of Apps

- Status: Accepted
- Date: 2026-07-20

## Context
The cluster contains workloads and infrastructure components that must be managed from a single GitOps repository.

Applyinf every Argo CD application manually would create repetitive work and could leave the cluster inconsistent with this repository.

## Decision
Use a root Argo CD as an application named `gitops-root`.

The root Application discover AppProjects and child apps under the `argocd` directory

the `bootstrap` and `install` directories are excluded because they contain the resources required to creatr Argo CD itself.

## Alternatives Considered

### Apply every Application manually
This approach works on the first time and is quite simple initially, but this may require repeated manual operations when a new application is added.

## Pros & Cons

### Positive
- New child apps are discovered from Git.
- Manual `kubectl apply` operations are minimized
- The repository remains the source of truth.

### Negative
- The root App must be bootstrapped manually for the firt time.
- A mistake under the `argocd` directory affect multiple child apps.
- Argo CD does not currently manage its own Helm.