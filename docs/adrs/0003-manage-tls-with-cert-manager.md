# ADR-0003: Manage TLS with cert-manager

- Status: Accepted
- Date: 2026-07-20

## Context
The cluster exposes multiple HTTPS routes through Traefik.

Manually requesting, storing and renewinf certificates would create repetitive work and increase the risk of expiration of the certificate.

The environement also requires separate Let's Encrypt behavior for dev and prod workloads.

## Decision
Use cert-manager to request and renew TLS certificates automatically.

Define two cluster issuers

| ClusterIssuer | Purpose |
|---|---|
| `letsencrypt-staging` | Development and certificate validation |
| `letsencrypt-prod` | Stable public applications |

Use the following issuer policy:

- Default and dev workloads must use `letsencrypt-staging`.
- Prod workloads must use `letsencrypt-prod`.
- Public Argo CD and Portainer routes use `etsencrypt-prod`.

Ingress resources reference the selected ClusterIssuer using `cert-manager.io/cluster-issuer` annotation.

Each Ingress defines its own `tls.secretName`. cert-manager creates the
corresponding `Certificate` resource and stores the certificate in a
namespaced Kubernetes Secret.

TLS Secrets are not copied between namespaces.

Certificate private keys, account credentials and Secret contents must never be
stored in Git.

## Alternatives Considered

### Manage certificates manually

Manual certificate management would require tracking expiration dates and
replacing Secrets before certificates expire.

### Use only the production Let's Encrypt issuer

Using the production issuer during development could consume Let's Encrypt rate
limits while configurations are still being tested.

### Share one TLS Secret between namespaces

Kubernetes Secrets are namespaced and cannot be referenced directly from
another namespace.

Copying the Secret would introduce additional synchronization and security
concerns.

### Use namespaced Issuers

Namespaced Issuers provide stronger isolation but would require duplicating
similar issuer configuration in every namespace.

The current single-cluster environment does not require that additional
complexity.

## Pros & Cons

### Positive

- Certificates are requested automatically.
- Certificates are renewed automatically.
- Staging can validate configuration without consuming production rate limits.
- ClusterIssuers provide one central certificate policy.
- Certificate configuration remains declarative.
- Private keys remain inside Kubernetes Secrets.

### Negative

- The cluster depends on cert-manager and the ACME service.
- Incorrect issuer configuration can prevent certificate creation.
- Each namespace requires its own TLS Secret.
- ACME challenges require the public domain and ports 80 and 443 to remain
  reachable.