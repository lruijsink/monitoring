# Loki

Log aggregation system storing logs of both [Alloy](../alloy/README.md) services
(Docker Compose, Kubernetes). Queried with LogQL via
[Grafana](../grafana/README.md) or REST.

- **Config:** [loki-config.yaml](loki-config.yaml)
- **Data:** stored in bind mount `data/` (gitignored)
- **Port:** `3100`, published on the host as well so Kubernetes can reach it
- **Retention:** 14 days

## Querying via API

The [Loki API](https://grafana.com/docs/loki/latest/reference/loki-http-api/)
queries log entries directly:

```shell
curl http://localhost:3100/loki/api/v1/query_range \
  --url-query 'query={project="my-app"} | logfmt | level="error"' \
  --url-query 'limit=10' |
  jq 'del(.data.stats)'
```

- `query`: LogQL query; use `logfmt`, `json`, or `pattern` for known log formats
- `jq 'del(.data.stats)'`: strips timing, cache, and chunk/index stats

**Optional parameters:**

- `since`: time interval to query, defaults to `1h`
- `limit`: maximum number of log entries returned, defaults to `100`
- `direction`: sort order, `forward` for ASC, defaults to `backward`

**Example output:**

```json
{
  "status": "success",
  "data": {
    "resultType": "streams",
    "result": [
      {
        "stream": {
          "project": "my-app",
          "service": "api",
          "level": "error",
          "msg": "timeout"
        },
        "values": [["1793055208303212427", "level=error msg=timeout"]]
      },
      {
        "stream": {
          "project": "my-app",
          "service": "api",
          "level": "error",
          "msg": "refused"
        },
        "values": [["1793055154838722888", "level=error msg=refused"]]
      }
    ]
  }
}
```

- `stream`: labels of the log stream, `logfmt` adds every parsed field (`level`,
  `msg`) as a label
- `values`: `[unix-nanosecond-timestamp, "log line"]`, newest first, one stream
  per distinct label set
