# SentinelOps

Laboratorio personal de monitorización de infraestructura Linux desarrollado con Docker Compose, Prometheus, Grafana y Node Exporter.

SentinelOps permite visualizar métricas del sistema Linux, incluyendo uso de CPU, memoria RAM, almacenamiento y tráfico de red.

[English](README.md) | [Español](README.es.md)

## Descripción

El proyecto está enfocado en la observabilidad de infraestructura, despliegue de servicios mediante contenedores y aplicación de medidas básicas de seguridad.

### Características

- Monitorización de CPU, RAM, disco y red.
- Recopilación de métricas mediante Prometheus cada 15 segundos.
- Dashboard de Grafana provisionado desde un archivo JSON versionado.
- Almacenamiento persistente para Prometheus y Grafana.
- Red bridge dedicada de Docker.
- Prometheus y Grafana accesibles únicamente desde localhost.
- Node Exporter configurado con montajes de solo lectura y capacidades Linux reducidas.
- Configuración de la contraseña de administrador de Grafana mediante variables de entorno.

## Arquitectura

```text
Host Linux
    |
Docker Compose
    |
Red bridge monitoring
    |
    +----------------+----------------+
    |                |                |
Prometheus         Grafana       Node Exporter
:9090              :3000             :9100
    |                |                 |
    |                |            Métricas del host
    |                |
    +------------ Fuente de datos
                     |
              Dashboard Grafana
```

## Tecnologías

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

El dashboard contiene seis paneles:

| Panel | Descripción |
|---|---|
| CPU Usage | Utilización de CPU del host |
| Memory Usage | Utilización de memoria RAM |
| Root Filesystem Usage | Espacio utilizado en el sistema de archivos raíz |
| Network Receive | Tráfico de entrada en `ens33` |
| Network Transmit | Tráfico de salida en `ens33` |
| Node Exporter Status | Disponibilidad de Node Exporter |

![Dashboard de SentinelOps](screenshots/03-sentinelops-dashboard.png)

## Estructura del proyecto

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

## Requisitos

- Sistema Linux con Docker Engine y Docker Compose.
- Git.
- Puertos locales `3000` y `9090` disponibles.
- Permisos para ejecutar comandos Docker.

## Instalación

### 1. Clonar el repositorio

```bash
git clone <REPOSITORY_URL>
cd SentinelOps
```

### 2. Configurar Grafana

```bash
cp .env.example .env
```

Edita `.env` y sustituye la contraseña de ejemplo por una contraseña única y robusta.

No subas el archivo `.env` a Git.

### 3. Iniciar los servicios

```bash
docker compose up -d
```

### 4. Comprobar el estado de los contenedores

```bash
docker compose ps
```

### 5. Acceder a los servicios

| Servicio | URL |
|---|---|
| Grafana | http://localhost:3000 |
| Prometheus | http://localhost:9090 |

Usuario de Grafana: `admin`

Contraseña: la configurada en `.env`.

## Configuración

### Prometheus

Archivo de configuración: `prometheus/prometheus.yml`

- Intervalo de scraping: 15 segundos.
- Intervalo de evaluación: 15 segundos.
- Objetivos: Prometheus y Node Exporter.
- Retención de datos: 7 días.

### Grafana

El dashboard se provisiona desde `grafana/dashboards/sentinelops.json`.

Al estar provisionado desde un archivo, las modificaciones deben realizarse en el JSON del proyecto, en lugar de guardarse desde la interfaz web de Grafana.

Los datos de Grafana se almacenan en un volumen persistente de Docker.

## Medidas de seguridad

- Los puertos de Grafana y Prometheus están vinculados a `127.0.0.1`.
- Los servicios se comunican mediante una red bridge dedicada.
- Node Exporter utiliza un sistema de archivos de contenedor de solo lectura.
- Node Exporter elimina capacidades Linux y activa `no-new-privileges`.
- Los montajes del sistema de archivos y recursos del host son de solo lectura.
- El registro público de usuarios de Grafana está deshabilitado.
- Las credenciales se proporcionan mediante un archivo de entorno excluido de Git.

Node Exporter necesita acceso a recursos del host para recopilar métricas. Sus privilegios y montajes deben revisarse antes de utilizar esta configuración en producción.

Este proyecto está diseñado como laboratorio local de monitorización, no como despliegue preparado para producción.

## Comandos útiles

Iniciar servicios:

```bash
docker compose up -d
```

Consultar estado:

```bash
docker compose ps
```

Consultar logs:

```bash
docker compose logs -f
```

Detener servicios sin eliminar los datos persistentes:

```bash
docker compose down
```

Validar la configuración de Compose:

```bash
docker compose config -q
```

## Capturas

| Componente | Captura |
|---|---|
| Objetivos de Prometheus | `screenshots/01-prometheus-targets.png` |
| Fuente de datos de Grafana | `screenshots/02-grafana-datasource.png` |
| Dashboard de monitorización | `screenshots/03-sentinelops-dashboard.png` |

## Aviso

SentinelOps es un proyecto educativo personal desarrollado para practicar monitorización de infraestructura Linux, administración de Docker y observabilidad.

No pretende sustituir una plataforma de monitorización o seguridad destinada a producción.
