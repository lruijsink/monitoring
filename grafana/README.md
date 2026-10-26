# Grafana

Dashboard and visualisation tools for [Prometheus](../prometheus/README.md)
metrics and [Loki](../loki/README.md) logs.

- **URL**: http://localhost:3000
- **Login**: `admin` / `admin`
- **Datasources:**
  - **Loki**: All logs, combines Docker Compose and Kubernetes
  - **Prometheus (Compose)**: Docker Compose Prometheus metrics
  - **Prometheus (k8s)**: Kubernetes Prometheus metrics

Dashboards are not bundled or versioned, define them in the UI or via the API.

Default port is `3000`, modify `grafana/compose.yaml` and re-up the service to
switch ports.
