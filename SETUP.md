# Setup

Instructions to set up monitoring for a Docker Compose or Kubernetes
application.

## Prerequisites

- **Metrics endpoint**, default is `/metrics` on port `8080`
- **Logs** written to stdout/stderr (no setup required)

## Docker Compose

Join the `monitoring` network and set the `prometheus.port` label:

```yaml
name: my-project # defines the `project` label, defaults to directory name

services:
  my-service:
    networks: [monitoring]
    labels:
      prometheus.port: "8080"
```

## Kubernetes

**1:** Give the Service a named port for the metrics endpoint, and a label for
the `ServiceMonitor` to select it by. Label itself is arbitrary, `app` here:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  labels:
    app: my-project
spec:
  selector:
    app: my-project
  ports:
    - name: metrics
      port: 8080
      targetPort: 8080
```

**2:** Add a `ServiceMonitor` in the same namespace

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: my-service
spec:
  selector:
    matchLabels:
      app: my-project
  endpoints:
    - port: metrics
      interval: 15s
```
