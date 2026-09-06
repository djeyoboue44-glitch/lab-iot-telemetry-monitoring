```markdown
<div align="center">

# 📡 IoT Telemetry Monitoring

### Plateforme de supervision full-stack — Infrastructure • IoT • Applicatif

![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![MQTT](https://img.shields.io/badge/MQTT-660066?style=for-the-badge&logo=mqtt&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)

</div>

---

## 📖 Description

Projet personnel de plateforme d'**observabilité full-stack**, déployée intégralement via Docker Compose. Il couvre trois domaines de supervision : **infrastructure**, **capteurs IoT** (via MQTT) et **application métier** (microservice instrumenté), avec un système d'alerting différencié par domaine.

---

## 🎯 Objectifs

- 🔗 Construire une chaîne de télémétrie complète : capteurs IoT → MQTT → collecte → visualisation
- 🖥️ Superviser simultanément l'infrastructure, les capteurs IoT et une application métier
- 🚨 Configurer des règles d'alerting différenciées par domaine (infra / IoT / applicatif)
- 🧪 Simuler des scénarios de panne réels pour valider la remontée d'alertes
- 🐍 Développer et exposer un microservice FastAPI instrumenté (Prometheus client)

---

## 🧰 Stack technique

| Composant | Rôle |
|---|---|
| 🐳 **Docker Compose** | Orchestration de l'ensemble des services |
| 📊 **Prometheus** | Collecte et stockage des métriques (scrape 15s) |
| 📈 **Grafana** | Visualisation et dashboards (Infrastructure, IoT Capteurs) |
| 🚨 **Alertmanager** | Routage des alertes par domaine vers des canaux dédiés |
| 🔄 **Telegraf** | Agent de collecte, pont MQTT → Prometheus |
| 📡 **Mosquitto** | Broker MQTT pour la remontée des données capteurs |
| 🖥️ **Node Exporter** | Métriques système (CPU, RAM, disque) |
| 📦 **cAdvisor** | Métriques des conteneurs Docker |
| 📥 **Pushgateway** | Réception de métriques ponctuelles |
| ⚡ **Microservice FastAPI** | Application instrumentée (requêtes, latence) |

---

## 🏗️ Architecture

```
     Capteurs IoT (simulateur Python)
              │  MQTT
              ▼
          Mosquitto
              │
              ▼
          Telegraf ──────► Prometheus ◄────── Node Exporter
                                │  ▲            cAdvisor
                                │  │            Pushgateway
                                │  │            Microservice FastAPI
                                ▼  │
                          Alertmanager
                                │
                ┌───────────────┼───────────────┐
          infra-channel    iot-channel      app-channel

                          Grafana (dashboards)
```

---

## 🚨 Règles d'alerting

<details>
<summary><b>8 règles réparties en 3 domaines — cliquer pour développer</b></summary>

| Domaine | Alerte | Condition | Sévérité |
|---|---|---|---|
| 🖥️ infra | `HostDown` | Cible injoignable | 🔴 critical |
| 🖥️ infra | `HighCPU` | Utilisation CPU élevée | 🟡 warning |
| 🖥️ infra | `HighMemory` | RAM utilisée > 80% pendant 5 min | 🟡 warning |
| 🖥️ infra | `DiskFull` | Disque utilisé > 90% pendant 10 min | 🔴 critical |
| 📡 iot | `SensorOffline` | Capteur injoignable pendant 2 min | 🔴 critical |
| 📡 iot | `BatteryLow` | Batterie < 15% pendant 5 min | 🟡 warning |
| ⚡ applicatif | `HighErrorRate` | Taux d'erreurs HTTP 5xx > 5% pendant 2 min | 🔴 critical |
| ⚡ applicatif | `HighLatency` | Latence p95 > 1s pendant 5 min | 🟡 warning |

</details>

---

## 📊 Dashboards Grafana

- **Infrastructure** — CPU Usage %, RAM Usage %, Conteneurs actifs
- **IoT Capteurs** — Température par capteur, Batterie des capteurs % (sensor-01, 02, 03)

---

## ✅ Cibles supervisées

Toutes les cibles Prometheus sont opérationnelles :

`prometheus` ✅ `pushgateway` ✅ `telegraf-iot` ✅ `node-exporter` ✅ `cadvisor` ✅ `microservice` ✅

---

## 🚀 Installation

```bash
git clone <repo-url>
cd telemetrie-project
docker compose up -d
```

Reconstruire uniquement le microservice après modification :

```bash
docker compose up -d --build microservice
```

---

## ▶️ Utilisation

| Service | URL |
|---|---|
| Prometheus | http://localhost:9090 |
| Alertmanager | http://localhost:9093 |
| Grafana | http://localhost:3000 |
| Métriques microservice | http://localhost:8000/metrics |

Lancer le simulateur de capteurs IoT :

```bash
python3 iot_simulator.py
```

---

## 🧪 Scénarios testés

- ⛔ Extinction volontaire de `node-exporter` → validation du déclenchement et de la résolution d'une alerte infra
- 🔌 Coupure de Mosquitto → observation de la reconnexion automatique de Telegraf
- 📡 Simulation de capteurs déconnectés → validation de l'alerte `SensorOffline`
- ✏️ Modification des règles d'alerting à chaud via `sed` + redémarrage du conteneur Prometheus
- 🔍 Vérification des 8 règles d'alerte (groupées par domaine) depuis Grafana Alerting

---

## 📁 Structure du projet

```
telemetrie-project/
├── docker-compose.yml
├── prometheus/
│   ├── prometheus.yml
│   └── rules/
│       └── alerts.yml
├── alertmanager/
│   └── alertmanager.yml
├── grafana/
│   ├── provisioning/
│   └── dashboards/
├── telegraf/
│   └── telegraf.conf
├── microservice/
│   ├── app.py
│   └── Dockerfile
└── iot_simulator.py
```

---

## 🖼️ Aperçu

<div align="center">

**Dashboard IoT Capteurs**
![Dashboard IoT Capteurs](screenshots/iot-capteurs-dashboard.png)

**Dashboard Infrastructure**
![Dashboard Infrastructure](screenshots/infrastructure-dashboard.png)

**Règles d'alerte Grafana**
![Règles d'alerte Grafana](screenshots/alert-rules-grafana.png)

**Targets Prometheus**
![Targets Prometheus](screenshots/prometheus-targets.png)

</div>

---

<div align="center">

*Projet réalisé dans le cadre d'une préparation à l'alternance Cybersécurité / Réseaux & Systèmes.*

</div>
```

Voilà une version plus soignée : badges technos en haut, tableaux, emojis, section repliable pour les règles d'alerte, et une section aperçu centrée. Pense à créer le dossier `screenshots/` et à y placer tes images avec les noms exacts utilisés dans les liens (`iot-capteurs-dashboard.png`, etc.), sinon les images n'apparaîtront pas.
