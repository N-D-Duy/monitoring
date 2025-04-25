# Monitoring Stack

This repository contains a comprehensive monitoring stack for containerized applications built with Docker Compose. It includes logging, metrics collection, and tracing capabilities through industry-standard tools.

## Stack Components

- **Prometheus** - Time series database for metrics collection and storage
- **Grafana** - Data visualization and dashboarding
- **Loki** - Log aggregation system
- **Promtail** - Log collector for Loki
- **Node Exporter** - Hardware and OS metrics exporter
- **Jaeger** - Distributed tracing system

## Prerequisites

- Docker and Docker Compose
- At least 2GB of RAM available for the stack
- Docker socket access for container log collection

## Getting Started

1. Clone this repository:
   ```
   git clone https://github.com/N-D-Duy/monitoring.git
   cd monitoring/common
   ```

2. Start the monitoring stack:
   ```
   docker compose up -d
   ```

3. Access the UIs:
   - Grafana: http://localhost:3000 (default credentials: admin/admin)
   - Prometheus: http://localhost:9090
   - Jaeger UI: http://localhost:16686
   - Loki (API only): http://localhost:3100/ready

## Configuration

### Prometheus

Prometheus is configured to scrape metrics from:
- Itself (`prometheus:9090`)
- Node Exporter (`node_exporter:9100`)
- Camera Server (`camera-server:8000`)

Configuration file: `./common/prometheus.yml`

### Loki

Loki is the log aggregation system with the following settings:
- Authentication disabled for development
- TSDB storage with filesystem backend
- Configurable rate limits through `tenant-limits.yaml`

Configuration file: `./common/loki-config.yml`

### Promtail

Promtail is configured to collect logs from:
- System logs in `/var/log/*log`
- Docker containers matching the `camera-server` pattern

Configuration file: `./common/promtail-config.yml`

### Jaeger

Jaeger provides distributed tracing capabilities with the following endpoints:
- UI: http://localhost:16686
- Collector: http://localhost:14268
- Zipkin compatible endpoint: http://localhost:9411

## Resource Limits

The stack has the following resource constraints:
- Loki is limited to 1GB memory and 2 CPU cores
- Loki ingestion rate: 10MB/s with 15MB burst size

## Adding Application Monitoring

### Metrics with Prometheus

Add your application to `prometheus.yml` under `scrape_configs`:

```yaml
- job_name: 'your-application'
  static_configs:
    - targets: ['your-application:port']
```

### Logs with Promtail/Loki

Modify the `promtail-config.yml` file to capture logs from your application:

```yaml
- job_name: your-application
  docker_sd_configs:
    - host: unix:///var/run/docker.sock
      refresh_interval: 5s
  relabel_configs:
    - source_labels: ['__meta_docker_container_name']
      regex: '.*your-application.*'
      action: keep
```

### Tracing with Jaeger

Configure your application to send traces to:
- HTTP: http://jaeger:14268/api/traces
- Zipkin: http://jaeger:9411/api/v2/spans

## Maintenance

- Prometheus data: Stored in the `prometheus_data` volume
- Loki data: Stored in the `loki_data` volume
- Grafana data: Stored in the `grafana_data` volume

To reset the stack completely:
```
docker-compose down -v
docker-compose up -d
```
## Contact
For any issues or feature requests, please contact:
- [Duy Nguyen](mailto:nguyenducduypc160903@gmail.com)