# Architecture Overview

## Purpose

This repo defines the desired state of the Silly Cats K3s cluster.

Argo CD compares the resources stored in this repo with the resources running in Kubernetes (K3s) and automatically synchronizes detected differences.

## Main Components

### K3s

K3s provides the Kubernetes control plane, container runtime and the default Traefik ingress controller.

### Argo CD

Argo CD monitors `main` branch of this repo and synchronizes the apps and the infrastructure resources.

the `gitops-root` applications is the main root Argo CD application that discover child apps stored under `argocd/apps` and the projects stored under `argocd/projects`.

### Traefik

Traefik receives HTTP and HTTPS requests for `desafio.bonam.cc` and routes them according to their URL paths.

### cert-manager

cert-manager requests and renews TLS certificates using Let's Encrypt.

Production workloads and publicly exposed management interfaces use the
production issuer. Development workloads use the staging issuer.

### Silly Cats

Silly Cats is deployed through a Helm chart with separate values files for dev and prod.

## Request Flow

1. A user sends an HTTPS request to DNS `desafio.bonam.cc`.
2. Traefik selects the matching Ingress route.
3. Traefik terminates TLS using Kubernetes Secret.
4. The request is fowarded to the appropiate Kubernetes Service.
5. The Service selects the application Pods using labels.