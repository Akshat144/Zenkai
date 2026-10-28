# Local observability

This is a development-only stack with pinned container versions. It provides real metrics collection, log ingestion, and a provisioned Grafana dashboard. It does not implement the university workflow or AI repair engine.

## Start

Requirements: Docker Engine and the Docker Compose plugin. Run from the repository root:

```bash
cp .env.example .env
# Edit .env and set your own GRAFANA_ADMIN_PASSWORD before the first start.
mkdir -p logs
docker compose config --quiet
docker compose up -d
```

Open [Grafana](http://localhost:3300/d/zenkai-overview) and sign in as `admin` with the password in `.env`. Prometheus and Loki are preconfigured as data sources. Existing Grafana volumes preserve the original login; changing `.env` later does not reset that account's password.

| Service | Local address | Readiness |
| --- | --- | --- |
| Grafana | http://localhost:3300 | `/api/health` |
| Prometheus | http://localhost:9090 | `/-/ready` |
| Loki | http://localhost:3100 | `/ready` |
| Alloy | http://localhost:12345 | `/-/ready` |

All published ports bind to the local machine only. Loki authentication is disabled for this isolated demo. Do not expose this configuration directly to a public network. Grafana's automatic plugin preinstallation and updates are disabled to avoid extra downloads during demo startup.

## Verify logs and metrics

Append one fictional event (this tests collection, not AI healing):

```bash
printf '%s\n' '{"level":"info","service":"zenkai-demo","event_id":"observability-smoke","step":"telemetry","outcome":"received","message":"Zenkai log pipeline is connected"}' >> logs/demo.log
```

Allow a few seconds for collection. In the provisioned dashboard, verify the event appears and the three Prometheus scrape targets have value `1`.

In Grafana Explore, useful queries are:

```promql
up
```

```logql
{service_name="zenkai-demo"} | json
```

Filter an incident by a JSON field rather than making its ID a label:

```logql
{service_name="zenkai-demo"} | json | incident_id="incident-demo-001"
```

Inspect service logs and configuration:

```bash
docker compose ps
docker compose logs --tail=50
docker compose exec prometheus promtool check config /etc/prometheus/prometheus.yml
docker compose exec alloy alloy validate /etc/alloy/config.alloy
```

## Application integration

- Write newline-delimited JSON into `logs/*.log`; Alloy mounts this directory read-only and forwards lines to Loki.
- Expose application metrics and add their network-reachable targets to `prometheus/prometheus.yml` when the application exists. Container `localhost` is the container itself, not the host.
- Use low-cardinality metric labels such as workflow type, step, and outcome. Do not label by student, event, or incident ID.
- Preserve immutable workflow state in a database; logs are evidence, not the transaction system.
- Add workflow backlog, failure, patch rejection, retry, and verified-recovery panels only when those metrics are actually emitted.

Alloy checkpoints and service data persist in Docker volumes. Prometheus retains three days of metrics. Loki currently has no automatic log-retention policy; use small fictional demo data and clean up deliberately when finished.

## Stop

```bash
docker compose down
```

This keeps persisted service data. `docker compose down -v` also deletes the stack's volumes, including Grafana settings and stored telemetry; use it only when you intend to reset this demo. Host files under `logs/` remain.

## References

- [Prometheus configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)
- [Loki configuration](https://grafana.com/docs/loki/latest/configure/)
- [Alloy file collection](https://grafana.com/docs/alloy/latest/reference/components/loki/loki.source.file/)
- [Grafana provisioning](https://grafana.com/docs/grafana/latest/administration/provisioning/)
