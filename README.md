# Monitoring

This repository contains a reusable monitoring stack for local development. It
is provided as-is, with macOS, [kind](https://kind.sigs.k8s.io/), and
[Docker Desktop](https://www.docker.com/products/docker-desktop/) in mind. Some
modification may be required for different setups.

## Requirements

- [Docker](https://docs.docker.com/get-docker/)
- Docker Compose v2.20.3 or later
- [Helm](https://helm.sh/docs/intro/install/)
- [Helmfile](https://helmfile.readthedocs.io/)
- Kubernetes cluster with port `30090` forwarded to the host (for the in-cluster
  Prometheus)
- Ports `3000`, `3100`, and `9090` are available
- Docker socket is available at `/var/run/docker.sock`

## Contents

- **Docker Compose**
  - [Grafana](grafana/README.md): observability dashboard on port `3000`
  - [Loki](loki/README.md): logging backend on port `3100`
  - [Alloy](alloy/README.md): telemetry collector
  - [Prometheus](prometheus/README.md): metrics backend on port `9090`
- **Kubernetes**
  - [Alloy](alloy/README.md): telemetry collector
  - [Prometheus](prometheus/README.md): via `kube-prometheus-stack` on port
    `30090`

## Install or update

From this directory, run:

```shell
docker compose up -d
helmfile apply
```

**Next steps:** Follow the [setup instructions](SETUP.md) to wire up a project.

_Note:_ `helmfile apply` updates charts but not CRDs. Recreate the cluster to
refresh them.

## Omissions

- Machine metrics, alerting, or traces
- Dashboards, `grafana/data/grafana.db` is gitignored
- Backups and retention periods
