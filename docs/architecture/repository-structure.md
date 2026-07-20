## Overview

This repository is the GitOps source of truth for the **Silly Cats** application K3s cluster. The application source code is maintained in a separate repository. All infrastructure management and deployment are handled via Argo CD.

---

## Main Structure

| Directory / File | Responsibility |
|---|---|
| `README.md` | Entry point with project summary and useful links. |
| `argocd/` | Declarative Argo CD configurations (Bootstrap, Apps, Projects). |
| `charts/` | Silly Cats Helm chart and environment-specific values files (`dev`, `prod`, `local`). |
| `infrastructure/` | Raw Kubernetes manifests (Namespaces, Traefik Ingresses, Let's Encrypt). |
| `docs/` | Architecture documentation, setup guides, and diagrams. |

---

## Key Components

*   **Argo CD Bootstrap:** The `argocd/bootstrap/root-app.yaml` file is the only one applied manually to initiate the GitOps flow. From there, Argo CD discovers and manages everything else.
*   **Argo CD Apps:** Divided into `infra/` (cert-manager, routing, essential namespaces) and `services/` (Silly Cats, Portainer).
*   **Argo CD Projects:** Define the permissions and security boundaries for each application within the cluster.
*   **Helm Values:** Container images are pinned by *digest* (SHA256) in the environment files (`values-dev.yaml`, `values-prod.yaml`) to ensure immutable deployments.

---

## Golden Rules (GitOps Boundaries)

*   **Git is the Source of Truth:** All cluster changes must be made via Pull Request to the `main` branch.
*   **No Secrets in Git:** Never commit credentials, passwords, or kubeconfig files.
*   **Active Self-Heal:** Any manual changes made directly in Kubernetes will be overwritten and reverted by Argo CD.

---

## Flow for New Resources

To add new infrastructure or workload:

1. Add the manifests in `infrastructure/` or create the Helm chart in `charts/`.
2. Configure the Namespace and permissions in the corresponding AppProject.
3. Declare the new Argo CD Application in the `argocd/apps/` folder.
4. Open a Pull Request and wait for automatic synchronization after merging into `main`.