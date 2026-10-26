# Alloy

Alloy scrapes telemetry, just logs for this project. Alloy collects the logs,
[Loki](../loki/README.md) stores them. It is deployed twice, as both a Docker
Compose and Kubernetes service. Both push to the same Loki backend, which runs
in Docker Compose.

## Docker Compose

- **Config:** `config.alloy`, sets the following labels (if present):
  - `project`: Compose project name
  - `service`: Compose service name
  - `container`: container name, minus leading `/`
- **Data:** stored in bind mount `data/` (gitignored)
- **Discovery:** logs are collected from all Docker containers via the mounted
  Docker socket. Notably, this differs from
  [Prometheus](../prometheus/README.md), which requires joining the `monitoring`
  network.

## Kubernetes

- **Config:** `helm-values.yaml`, sets the following labels:
  - `namespace`: pod namespace
  - `pod`: pod name
  - `container`: container name
- **Discovery:** logs are collected from all pods in all namespaces via the
  Kubernetes API
- **Loki connection:** Loki runs in Docker Compose, outside the cluster, Alloy
  requires port `3100` to be published on the host to reach it.
