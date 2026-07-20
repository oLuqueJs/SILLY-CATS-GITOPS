# SILLY CATS GITOPS

[![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](#)
[![Helm Charts](https://img.shields.io/badge/Helm%20Charts-0F1689?style=for-the-badge&logo=helm&logoColor=white)](#)
[![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)](#)
[![Portainer](https://img.shields.io/badge/Portainer-13BEF9?style=for-the-badge&logo=portainer&logoColor=white)](#)
[![K3s](https://img.shields.io/badge/K3s-FFC61C?style=for-the-badge&logo=k3s&logoColor=black)](#)

GitOps source of truth for Silly Cats Application repository and for the infrastructure running on a single-node K3s ubuntu cluster.

## Overview

This repository manages:

- Silly Cats development and production deployments
- Argo CD applications (app of apps)
- cert-manager and Let's Encrypt issuers
- Traefik ingress resources
- Portainer
- Kubernetes namespaces

The application source code repository:
[Silly Cats repository](https://github.com/oLuqueJs/SILLY-CATS).

## Environments

| Component | Namespace | URL |
|---|---|---|
| Silly Cats production | `silly-cats-prod` | `https://desafio.bonam.cc/` |
| Silly Cats development | `silly-cats-dev` | `https://desafio.bonam.cc/dev/` |
| Argo CD | `argocd` | `https://desafio.bonam.cc/argocd/` |
| Portainer | `portainer` | `https://desafio.bonam.cc/portainer/` |

## GitOps Delivery Flow
Application releases are bu ilt in the Silly Cats repository. The release workflow publishes an container image and opens a PR to this repository.

A deployment happens only after the GitOps PR is reviewed and merged


## Repository Structure

- `argocd/`: Argo CD bootstrap, projects and apps
- `charts/`: application Helm charts and envs values
- `infrastructure`: cluster-level manifests
- `docs/`: architecture, setup and ADRs

## Security
Secrets, kubeconfig files, PKs, tokens and admin passwords must never be committed to this repo.