# Local Setup

## Purpose
This doc explains how to validate and optionally deploy the Silly Cats Helm
chart to a local Kubernetes cluster.

Local deployment does not use Argo CD.

## Prerequisites
- Git
- Helm 3 (or Helm 4)
- kubectl
- an local kubernetes cluester (k3s recommended)
- Traefik, when testing the local Ingress

The provided local values expect you to use orbstack.

The local hostname value is:
`silly-cats.k8s.orb.local`.

Update the local values if your local Kubernetes environment uses a different hostname.

## Clone the Repository

```bash
git clone https://github.com/oLuqueJs/SILLY-CATS-GITOPS.git
cd SILLY-CATS-GITOPS
```

## Validate the Chart

```bash
helm lint charts/silly-cats \
  -f charts/silly-cats/values-local.yaml
```

Render the manifests without applying them:

```bash
helm template silly-cats-local charts/silly-cats \
  --namespace silly-cats-local \
  -f charts/silly-cats/values-local.yaml
```

## Install Locally

```bash
helm upgrade --install silly-cats-local charts/silly-cats \
  --namespace silly-cats-local \
  --create-namespace \
  -f charts/silly-cats/values-local.yaml
```

## Verify the Deployment

```bash
kubectl -n silly-cats-local rollout status deployment/silly-cats-local
kubectl -n silly-cats-local get deployment,pod,service,ingress
```

## Access the Application

Open:
`http://silly-cats.k8s.orb.local`