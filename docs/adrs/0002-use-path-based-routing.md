# ADR-0002: Use Path-Based Routing on a Single Domain

- Status: Accepted
- Date: 2026-07-20

## Context

The K3s cluster exposes multiple apps through the public domain `desafio.bonam.cc.`

Creating additional DNS records or subdomains was not available for this environment.
Cause we don't have an wildcard DNS and we can't manage the DNS right now.

The cluster must expose the prod applications, dev application, Argo CD and Portainer using the same hostname.

## Decision 

Use path-base routing with the default K3s Traefik ingress controller.
Use middlewares for strip-prefix

The public routes are: 

| Application | Path |
|---|---|
| Silly Cats production | `/` |
| Silly Cats development | `/dev` |
| Argo CD | `/argocd` |
| Portainer | `/portainer` |

Use standard Kubernetes `Ingress` resources with `pathType: Prefix`.

Applications handle their external paths differently:

- Argo CD is configured with `server.rootpath` and `server.basehref`.
- Argo CD does not use a `StripPrefix` Middleware.
- Portainer is configured with `--base-url=/portainer`.
- Portainer uses a Traefik `StripPrefix` Middleware.
- Silly Cats development uses a `StripPrefix` Middleware for `/dev`.
- Silly Cats production is served from the root path.

Traefik terminates TLS before forwarding requests to the internal Services.

## Alternatives Considered

### Use one subdomain per application
Subdomains would provide clearer isolation, but the required DNS records cannot
be created in this environment in development time. ik i could asked ok :)

### Expose applications with NodePort Services
NodePort would expose the apps through different ports instead of an standard HTTPS URLs.
This would provide a less friendly user XP and increase the number of publicly exposed ports.
And of course, we would needed these ports opened at VPS firewall.

### Use SSH tunnel to expose internally the localhost
This would work and would be more secure than the path-based, but this alternative requires an manual setup every time, and the SSH tunnel closes after some time when inactive.

## Pros & Cons

### Positive

- Only one DNS record is required.
- ALl applications use standard HTTPS. URLs.
- The existing K3s Traefik installation can manage every route.
- No additional load balancer or ingress controller is required
- Routing configuration remains declarative and stored in Git.

### Negative
- apps must support a base path or require a Middleware.
- Incorrect path rules may cause one app to intercept another route
- Portainer only works at `desabio.bonam.cc/portainer/` and not `desafio.bonam.cc/portainer` (trailing slash)
- Path-based routing is more coupled than using independent subdomains.
