# GitOps Delivey Flow

## Overview
The application source code and the deployment configs are maintained in separate repositories.

## Detailed Flow

1. A change is merged into the `main` branch of the Silly Cats repo.
2. semantic-release determines if a new version should be created.
3. The CI workflow builds and publishes an AMD64 & ARM64 container image.
4. The workflow updated the image tag and digest in this repo.
5. An automated GitOps PR is openend.
6. The manual review is required, then merge the PR here.
7. ArgoCD detects the new Git revision and synchronizes the dev and prod apps.
8. Kubernetes performs a rolling update of the Deployments.

## Important
Argo CD monitors this GitOps repository. It does not monitor the application source repository, or the container registry diectly.

Publishing a new image does not trigger a deployment, unless the image in this repository is updated and merget to `main`.

The application are piined by image digest to run the exact image produced by CI.