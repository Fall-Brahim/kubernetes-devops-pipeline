# 🚀 DevOps CI/CD & Kubernetes — Infrastructure Complète

![Pipeline](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?style=flat&logo=github-actions&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Hub-2496ED?style=flat&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.29-326CE5?style=flat&logo=kubernetes&logoColor=white)
![Flannel](https://img.shields.io/badge/CNI-Flannel-purple?style=flat)
![Proxmox](https://img.shields.io/badge/Infrastructure-Proxmox-E57000?style=flat&logo=proxmox&logoColor=white)
![Status](https://img.shields.io/badge/Pipeline-passing-63fcb7?style=flat)

---

![Architecture CI/CD & Kubernetes](images/devops_animated.gif)

---

## 📌 Introduction

Ce projet met en place une **infrastructure DevOps complète** allant du code source jusqu'au déploiement automatique dans un cluster Kubernetes self-hosted sur Proxmox.

L'objectif est de démontrer une maîtrise de la chaîne DevOps complète :

```
Code → Build → Push → Deploy → Monitor
```

Chaque `git push` sur la branche `main` déclenche automatiquement :
1. Le **build** de l'image Docker
2. Le **push** vers Docker Hub (`:latest` + `:sha`)
3. Le **déploiement** rolling update sur le cluster Kubernetes
4. La **vérification** de santé des pods

---

## 🏗 Architecture

| Couche | Technologie | Rôle |
|--------|------------|------|
| Source Control | GitHub | Versioning & déclencheur CI/CD |
| CI/CD | GitHub Actions + Self-hosted Runner | Automatisation du pipeline |
| Containerisation | Docker | Build & packaging de l'application |
| Registry | Docker Hub | Stockage des images |
| Orchestration | Kubernetes v1.29 (kubeadm) | Déploiement & scaling |
| Réseau | Flannel CNI (10.244.0.0/16) | Communication inter-pods |
| Infrastructure | Proxmox VE | Virtualisation self-hosted |
| Monitoring | Prometheus + Grafana | Métriques & dashboards |

---

## 🖥 Infrastructure Kubernetes

```
k8s-master     192.168.100.30   control-plane   (API Server, Scheduler, etcd)
k8s-worker-1   192.168.100.31   worker node     (3 pods applicatifs)
k8s-worker-2   192.168.100.32   worker node     (3 pods applicatifs)
```

- **6 replicas** répartis sur 2 workers
- **NodePort 30080** → exposition de l'application
- **Rolling Update** — zéro downtime lors des déploiements

---

## ⚙️ Pipeline CI/CD

```yaml
git push main
    │
    ▼
┌─────────────────────────┐
│  GitHub Actions         │
│  Build & Push           │  ← runs-on: ubuntu-latest
│  fall948/myapp:sha      │
│  fall948/myapp:latest   │
└────────────┬────────────┘
             │ needs: build-and-push
             ▼
┌─────────────────────────┐
│  Self-hosted Runner     │
│  Deploy to K8s          │  ← runs-on: self-hosted (k8s-master)
│  kubectl set image      │
│  kubectl rollout status │
└─────────────────────────┘
```

---

## 📁 Structure du Projet

```
myapp/
├── app/
│   ├── main.py              # Application Flask
│   └── requirements.txt
├── k8s/
│   ├── deployment.yaml      # 3 replicas, RollingUpdate, health probes
│   └── service.yaml         # NodePort 30080
├── .github/
│   └── workflows/
│       └── ci-cd.yml        # Pipeline GitHub Actions
├── assets/
│   └── devops_animated.gif  # Schéma d'architecture animé
├── Dockerfile
└── README.md
```

---

## 🚀 Démarrage Rapide

```bash
# Cloner le repo
git clone https://github.com/Fall-Brahim/kubernetes-devops-pipeline
cd kubernetes-devops-pipeline

# Tester l'application en local
docker build -t myapp:latest .
docker run -p 5000:5000 myapp:latest

# Accéder à l'app
curl http://localhost:5000/health
```

**Secrets GitHub requis :**
| Secret | Description |
|--------|-------------|
| `DOCKER_USERNAME` | Identifiant Docker Hub |
| `DOCKER_TOKEN` | Token d'accès Docker Hub |

---

## 📊 Monitoring

Stack de monitoring déployé sur le cluster :
- **Prometheus** — collecte des métriques (CPU, RAM, pods)
- **Grafana** — dashboards visuels (IDs : 315, 6417, 1860)
- **kube-prometheus-stack** via Helm

---

## 🗺 Roadmap

- [x] Application Flask containerisée
- [x] Pipeline GitHub Actions (build & push)
- [x] Cluster Kubernetes 3 nodes (kubeadm)
- [x] Self-hosted runner connecté au cluster
- [x] Deploy job automatisé
- [ ] Prometheus + Grafana
- [ ] GitLab self-hosted (Partie 2)
- [ ] Jenkins pipeline
- [ ] Docker Registry privé

---

