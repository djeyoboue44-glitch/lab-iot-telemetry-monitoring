# IoT Telemetry Monitoring

## Description

Projet personnel de plateforme de supervision (observabilité) réalisé sous Docker Compose, intégrant la collecte de métriques IoT via MQTT, un microservice applicatif instrumenté, et une chaîne complète de monitoring et d'alerting.

## Objectifs

- Mettre en place une chaîne de télémétrie complète : capteurs IoT → MQTT → collecte → visualisation
- Superviser à la fois l'infrastructure, les capteurs IoT et une application métier
- Configurer des règles d'alerting différenciées par domaine (infra / IoT / applicatif)
- Simuler des scénarios de panne pour valider la remontée d'alertes

## Environnement

- **Kali Linux** : machine hôte, exécution de la stack Docker
- **Prometheus** : collecte et stockage des métriques (scrape 15s)
- **Grafana** : visualisation et dashboards
- **Alertmanager** : routage des alertes par domaine vers des canaux dédiés
- **Telegraf** : agent de collecte, MQTT → Prometheus
- **Mosquitto** : broker MQTT pour la remontée des données capteurs
- **Node Exporter** : métriques système (CPU, RAM, disque)
- **cAdvisor** : métriques des conteneurs Docker
- **Pushgateway** : réception de métriques ponctuelles
- **Microservice FastAPI** : application instrumentée (compteur de requêtes, latence)

## Architecture

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
              ┌───────────┼───────────┐
        infra-channel  iot-channel  app-channel
                          
                    Grafana (dashboards)
```

## Règles d'alerting

Les alertes sont regroupées en trois domaines, chacun routé vers un canal dédié dans Alertmanager :

| Domaine | Alerte | Condition |
|---|---|---|
| infra | HighMemoryUsage | RAM utilisée > 80% pendant 5 min |
| infra | DiskFull | Disque utilisé > 90% pendant 10 min |
| iot | SensorOffline | Capteur injoignable pendant 2 min |
| iot | BatteryLow | Batterie < 15% pendant 5 min |
| applicatif | HighErrorRate | Taux d'erreurs HTTP 5xx > 5% pendant 2 min |
| applicatif | HighLatency | Latence p95 > 1s pendant 5 min |

## Installation

```bash
git clone <repo-url>
cd telemetrie-project
docker compose up -d
```

## Utilisation

- Prometheus : http://localhost:9090
- Alertmanager : http://localhost:9093
- Grafana : http://localhost:3000
- Métriques microservice : http://localhost:8000/metrics

Lancer le simulateur de capteurs IoT :

```bash
python3 iot_simulator.py
```

## Scénarios testés

- Extinction volontaire de `node-exporter` pour valider le déclenchement et la résolution d'une alerte infra
- Coupure de Mosquitto pour observer la reconnexion automatique de Telegraf
- Simulation de capteurs déconnectés pour valider l'alerte `SensorOffline`

## Structure du projet

```
telemetrie-project/
├── docker-compose.yml
├── prometheus/
│   ├── prometheus.yml
│   └── rules/
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
```

Copie-colle tout ce bloc directement dans ton fichier `README.md` sur GitHub.
