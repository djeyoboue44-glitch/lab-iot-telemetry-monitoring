
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

## 🚀 Installation

```bash
git clone <repo-url>
cd telemetrie-project
docker compose up -d
```

---

## ▶️ Utilisation

| Service | URL |
|---|---|
| Prometheus | http://localhost:9090 |
| Alertmanager | http://localhost:9093 |
| Grafana | http://localhost:3000 |
| Métriques microservice | http://localhost:8000/metrics |

```bash
python3 iot_simulator.py
```

---

## 📁 Structure du projet

```
telemetrie-project/
├── docker-compose.yml
├── prometheus/
│   ├── prometheus.yml
│   └── rules/alerts.yml
├── alertmanager/alertmanager.yml
├── grafana/{provisioning,dashboards}/
├── telegraf/telegraf.conf
├── microservice/{app.py,Dockerfile}
└── iot_simulator.py
```

---

## 🖼️ Aperçu du projet

### 🏗️ Mise en place de l'infrastructure

**1. Arborescence du projet & docker-compose (services grafana/alertmanager/mosquitto/telegraf)**
![1](1-Cahier%20telemetrie%20.PNG)

**2. Configuration Prometheus (prometheus.yml — scrape configs)**
![2](2-Cahier%20telemetrie%20.PNG)

**3. Règles d'alerte — domaines infra & iot**
![3](3-Cahier%20telemetrie%20.PNG)

**4. Règles d'alerte — domaine applicatif & configuration Telegraf**
![4](4-Cahier%20telemetrie%20.PNG)

**5. Configuration Alertmanager (routes & receivers par domaine)**
![5](5-Cahier%20telemetrie%20.PNG)

**6. Microservice FastAPI instrumenté (app.py) & Dockerfile**
![6](6-Cahier%20telemetrie%20.PNG)

**7. Dockerfile microservice & simulateur IoT (iot_simulator.py)**
![7](7-Cahier%20telemetrie.PNG)

### 🐳 Déploiement Docker Compose

**8. Installation docker-compose & `docker compose up -d`**
![8](8-cahier%20telemetrie.PNG)

**9. Vérification des conteneurs (`docker compose ps`)**
![9](9-%20Cahier%20telemetrie%20.PNG)

**10. Graphe Prometheus — métriques collectées**
![10](Cahier%20telemetrie%2010%20.PNG)

**11. Alertes actives dans Alertmanager**
![11](Cahier%20telemetrie%2011.PNG)

### 📊 Dashboards Grafana

**12. Interface d'accueil Grafana**
![12](Cahier%20telemetrie%2012.PNG)

**13. Simulateur IoT en cours d'exécution (publication MQTT)**
![13](Cahier%20telemetrie%2013.PNG)

**14. Logs Prometheus & Telegraf**
![14](Cahier%20telemetrie%2014.PNG)

### 🧪 Tests & scénarios de panne

**15. Test d'arrêt de node-exporter (déclenchement d'alerte)**
![15](cahier%20telemetrie%2015.PNG)

**16. Dashboard Grafana Infrastructure (CPU / RAM / Conteneurs actifs)**
![16](cahier%20telemetrie%2016.PNG)

**17. Vérification des conteneurs après redémarrage**
![17](cahier%20telemetrie%2017.PNG)

**18. Logs Telegraf — reconnexion automatique à Mosquitto**
![18](cahier%20telemetrie%2018.PNG)

**19. Test du simulateur (site-A) — publication des capteurs**
![19](cahier%20telemetrie%2019.PNG)

**20. Logs Docker du conteneur Telegraf**
![20](cahier%20telemetrie%2020.PNG)

### 🔔 Alerting avancé

**21. Vue complète des règles d'alerte (infra / iot / applicatif)**
![21](cahier%20telemetrie%2021.PNG)

**22. Modification des règles d'alerte à chaud (`sed` + `grep`)**
![22](cahier%20telemetrie%2022.PNG)

**23. Liste des règles d'alerte dans Grafana Alerting**
![23](cahier%20telemetrie%2023.PNG)

---

<div align="center">

*Projet réalisé dans le cadre d'une préparation à l'alternance Cybersécurité / Réseaux & Systèmes.*

</div>

Aussi : les noms de fichiers contiennent des espaces, donc j'ai encodé les espaces en `%20` dans les liens Markdown pour garantir l'affichage correct sur GitHub.
