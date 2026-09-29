# SentinelOps

A self-hosted Linux infrastructure monitoring lab built with Docker Compose, Prometheus, Grafana and Node Exporter.

SentinelOps provides centralized visibility into CPU utilization, memory usage, filesystem capacity and network traffic on a Linux host.

## Overview

The project focuses on infrastructure observability, container deployment and basic security hardening.

### Features

- Host-level CPU, RAM, disk and network monitoring.
- Prometheus metrics collection every 15 seconds.
- Grafana dashboard provisioned from a version-controlled JSON file.
- Persistent storage for Prometheus and Grafana.
- Dedicated Docker bridge network.
- Prometheus and Grafana bound to localhost.
- Node Exporter configured with read-only mounts and reduced Linux capabilities.
- Environment-based Grafana administrator password configuration.

## Architecture

```text
Linux Host
    |
Docker Compose
    |
monitoring bridge network
    |
    +----------------+----------------+
    |                |                |
Prometheus         Grafana       Node Exporter
:9090              :3000             :9100
    |                |                 |
    |                |            Host metrics
    |                |
    +------------ Data source
                     |
               Grafana Dashboard
```

## Technology Stack

- Arch Linux
- Docker Engine
- Docker Compose
- Prometheus
- Grafana
- Node Exporter
- PromQL
- YAML
- JSON

## Dashboard

The dashboard includes six panels:

| Panel | Description |
|---|---|
| CPU Usage | Host CPU utilization |
| Memory Usage | RAM utilization |
| Root Filesystem Usage | Root filesystem capacity used |
| Network Receive | Incoming traffic on `ens33` |
| Network Transmit | Outgoing traffic on `ens33` |
| Node Exporter Status | Exporter availability |

![SentinelOps Dashboard](screenshots/03-sentinelops-dashboard.png)

## Project Structure

```text
SentinelOps/
├── compose.yaml
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   ├── dashboards/
│   │   └── sentinelops.json
│   └── provisioning/
│       └── dashboards/
│           └── provider.yml
├── screenshots/
├── .env.example
├── .gitignore
└── README.md
```

## Requirements

- Linux host with Docker Engine and Docker Compose.
- Git.
- Available localhost ports `3000` and `9090`.
- Permissions to run Docker commands.

## Deployment

### 1. Clone the repository

```bash
git clone <REPOSITORY_URL>
cd SentinelOps
```

### 2. Configure Grafana

```bash
cp .env.example .env
```

Edit `.env` and replace the example password with a unique, strong password.

Do not commit `.env`.

### 3. Start the stack

```bash
docker compose up -d
```

### 4. Check container status

```bash
docker compose ps
```

### 5. Access the services

| Service | URL |
|---|---|
| Grafana | http://localhost:3000 |
| Prometheus | http://localhost:9090 |

Grafana username: `admin`

Password: the value configured in `.env`.

## Configuration

### Prometheus

Configuration: `prometheus/prometheus.yml`

- Scrape interval: 15 seconds.
- Evaluation interval: 15 seconds.
- Targets: Prometheus and Node Exporter.
- Data retention: 7 days.

### Grafana

The dashboard is provisioned from `grafana/dashboards/sentinelops.json`.

Because it is file-provisioned, dashboard changes should be made in the JSON source rather than saved through the Grafana UI.

Grafana data is stored in a persistent Docker volume.

## Security Considerations

- Grafana and Prometheus ports are bound to `127.0.0.1`.
- Services communicate through a dedicated Docker bridge network.
- Node Exporter has a read-only container filesystem.
- Node Exporter drops Linux capabilities and enables `no-new-privileges`.
- Host filesystem and system metric mounts are read-only.
- Grafana public sign-up is disabled.
- Credentials are supplied through an environment file excluded from Git.

Node Exporter requires access to host resources to collect host-level metrics. Its privileges and mounts should be reviewed before deploying this configuration on production systems.

This project is intended as a local monitoring lab, not a production-ready deployment.

## Useful Commands

Start services:

```bash
docker compose up -d
```

View status:

```bash
docker compose ps
```

Follow logs:

```bash
docker compose logs -f
```

Stop services without deleting persistent data:

```bash
docker compose down
```

Validate Compose configuration:

```bash
docker compose config -q
```

## Screenshots

| Component | Screenshot |
|---|---|
| Prometheus targets | `screenshots/01-prometheus-targets.png` |
| Grafana data source | `screenshots/02-grafana-datasource.png` |
| Monitoring dashboard | `screenshots/03-sentinelops-dashboard.png` |

## Disclaimer

SentinelOps is a personal educational project developed for Linux infrastructure monitoring, Docker administration and observability practice.

It is not intended to replace a production monitoring or security platform.
