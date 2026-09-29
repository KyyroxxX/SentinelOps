# 🔴 SENTINELOPS

### Laboratorio de observabilidad de infraestructura Linux

**Visualiza el sistema. Entiende la señal.**

Laboratorio de monitorización autohospedado y reproducible para Linux, desarrollado con Docker Compose, Prometheus, Grafana y Node Exporter.

[![Docker Compose](https://img.shields.io/badge/Docker_Compose-2496ED?style=flat-square&logo=docker&logoColor=white)](compose.yaml) [![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=flat-square&logo=prometheus&logoColor=white)](prometheus/prometheus.yml) [![Grafana](https://img.shields.io/badge/Grafana-F46800?style=flat-square&logo=grafana&logoColor=white)](grafana/)

[🇬🇧 English](README.md) · [Arquitectura](#-arquitectura) · [Instalación rápida](#-instalación-rápida) · [Seguridad](#-seguridad)

---

## ◈ Objetivo

SentinelOps ofrece visibilidad centralizada de un host Linux. Node Exporter expone métricas, Prometheus las recopila y almacena, y Grafana las presenta en un dashboard provisionado desde configuración versionada.

> **Tipo:** Laboratorio educativo personal · **Despliegue:** Docker Compose · **Enfoque:** Linux, observabilidad y operaciones de infraestructura

## ◈ Funcionalidades

| Área | Implementación |
|---|---|
| Métricas del host | CPU, RAM, filesystem y red |
| Recopilación | Prometheus · intervalo de 15 segundos |
| Visualización | Dashboard Grafana provisionado desde JSON |
| Persistencia | Volúmenes Docker |
| Exposición | Grafana y Prometheus vinculados a `127.0.0.1` |
| Hardening | Node Exporter con filesystem de solo lectura, capacidades eliminadas y `no-new-privileges` |
| Configuración | YAML y contraseña de administrador mediante entorno |

## ◈ Dashboard

<div align="center">

![Dashboard de Grafana de SentinelOps](screenshots/03-sentinelops-dashboard.png)

*Métricas del host en una única vista de Grafana.*

</div>

### Evidencias

| Objetivos de Prometheus | Fuente de datos de Grafana |
|---|---|
| ![Objetivos de Prometheus](screenshots/01-prometheus-targets.png) | ![Fuente de datos de Grafana](screenshots/02-grafana-datasource.png) |

## ◈ Arquitectura

```text
                         HOST LINUX
                             │
                       DOCKER COMPOSE
                             │
                    Red bridge monitoring
              ┌──────────────┼──────────────┐
              │              │              │
         Prometheus        Grafana     Node Exporter
           :9090            :3000          :9100
              │              ▲              │
              └── métricas ──┘         métricas host
                             │
                    Dashboard provisionado
```

## ◈ Instalación rápida

### Requisitos
- Host Linux con Docker Engine y Docker Compose
- Git
- Puertos locales `3000` y `9090` disponibles

### 1 — Clonar
```bash
git clone https://github.com/KyyroxxX/SentinelOps.git
cd SentinelOps
```

### 2 — Configurar credenciales
```bash
cp .env.example .env
```
Edita `.env` y establece una contraseña robusta y única para el administrador de Grafana. No subas `.env` a Git.

### 3 — Validar e iniciar
```bash
docker compose config -q
docker compose up -d
docker compose ps
```

### 4 — Acceder
| Servicio | Dirección local | Autenticación |
|---|---|---|
| Grafana | [localhost:3000](http://localhost:3000) | `admin` / contraseña de `.env` |
| Prometheus | [localhost:9090](http://localhost:9090) | Sin autenticación configurada |

## ◈ Estructura
```text
SentinelOps/
├── compose.yaml
├── prometheus/prometheus.yml
├── grafana/
│   ├── dashboards/sentinelops.json
│   └── provisioning/
├── screenshots/
├── .env.example
├── .gitignore
├── README.md
└── README.es.md
```

## ◈ Comandos de operación
| Tarea | Comando |
|---|---|
| Estado | `docker compose ps` |
| Seguir logs | `docker compose logs -f` |
| Reiniciar | `docker compose restart` |
| Detener conservando datos | `docker compose down` |
| Validar Compose | `docker compose config -q` |

Para eliminar también los datos persistentes, ejecuta `docker compose down -v`. **Esto borra los datos de los volúmenes.**

## ◈ Configuración
- Configuración Prometheus: `prometheus/prometheus.yml`
- Intervalos de scraping/evaluación: 15 segundos
- Retención: 7 días
- Fuente del dashboard: `grafana/dashboards/sentinelops.json`
- El dashboard se provisiona desde archivo; modifica su JSON en Git.
- Grafana y Prometheus solo se publican en loopback.

## ◈ Seguridad
- Mantén `.env` privado; usa `.env.example` para valores ficticios.
- Las interfaces web están pensadas para acceso local, no para exposición pública directa.
- Node Exporter necesita visibilidad del host para recopilar métricas. Revisa sus montajes y permisos antes de adaptar el proyecto.
- Este repositorio es un laboratorio de aprendizaje, **no una plataforma endurecida para producción**.

## ◈ Hoja de ruta
- [x] Stack de monitorización con Docker Compose
- [x] Recopilación Prometheus y provisioning de Grafana
- [x] Persistencia de métricas y datos del dashboard
- [x] Interfaces web limitadas a localhost
- [x] Documentación y capturas
- [ ] Validación CI de Compose y configuraciones
- [ ] Demo reproducible paso a paso
- [ ] Reglas de alertas y casos de prueba documentados

---

<div align="center">

**Creado por [KyyroxxX](https://github.com/KyyroxxX)**  
Linux · Infraestructura · Observabilidad

<sub>Construye para aprender. Mide lo que importa.</sub>

</div>
