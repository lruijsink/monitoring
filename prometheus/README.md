# Prometheus

Two instances are available; one running via Docker Compose, the other in
Kubernetes.

## Docker Compose

- Runs as the `prometheus` service on port `9090`, defined in `compose.yaml`
- Auto-discovers containers with a `prometheus.port` label in the `monitoring`
  network
- Scraping these adds standard labels, configured by `prometheus.yaml`:
  - `project`: Compose project name (defined by `name`, or directory name if
    absent)
  - `service`: Compose service name
  - `__address__`: container IP + `prometheus.port`
- Available in [Grafana](../grafana/README.md) as **Prometheus (Compose)**

## Kubernetes

- Uses the `kube-prometheus-stack` chart
- Scrapes anything selected by a `ServiceMonitor` or `PodMonitor`, in any
  namespace
- Uses NodePort `30090`
- Alertmanager is disabled, Grafana runs in Docker Compose instead
- Available in [Grafana](../grafana/README.md) as **Prometheus (k8s)**

### Exposing the NodePort

Grafana expects port `30090` to be forwarded to the host machine. The method for
this differs per Kubernetes engine. For kind, set the following cluster config
(requires cluster recreate):

```yaml
- role: control-plane
  extraPortMappings:
  - containerPort: 30090 # NodePort
    hostPort: 30090 # port used by Grafana data source
    listenAddress: 127.0.0.1
```

## Querying via API

The
[Prometheus HTTP API](https://prometheus.io/docs/prometheus/latest/querying/api/)
is identical for both instances, only the port differs:

- Docker Compose: `9090`
- Kubernetes: `30090`

### Instant query

```shell
curl http://localhost:9090/api/v1/query \
  --url-query 'query=up{project="my-app"}' |
  jq '.data.result[]'
```

- `query`: PromQL expression, evaluated at a single point in time
- `jq '.data.result[]'`: selects an object per series, with fields:
  - `metric`: labels
  - `value`: array of `[unix-timestamp, "sample"]`

**Example output:**

```json
{
  "metric": {
    "__name__": "up",
    "project": "my-app",
    "service": "api"
  },
  "value": [1793049967.0, "1"]
}
```

### Range query

```shell
curl http://localhost:9090/api/v1/query_range \
  --url-query 'query=rate(http_requests_total[5m])' \
  --url-query "start=$(date -v-1H +%s)" \
  --url-query "end=$(date +%s)" \
  --url-query 'step=1m' |
  jq '.data.result[]'
```

- `start`, `end`: RFC3339/Unix timestamps (note `date -v-1H` is macOS-specific)
- `step`: resolution, as a duration (`1m`) or seconds
- `jq '.data.result[]'`: selects an object per series, with fields:
  - `metric`: labels
  - `values`: array of `[unix-timestamp, "sample"]`, one per step

**Example output:**

```json
{
  "metric": {
    "project": "my-app",
    "service": "api"
  },
  "values": [
    [1793049907, "0.42"],
    [1793049967, "0.45"],
    [1793050027, "0.51"]
  ]
}
```
