<div align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=28&duration=4000&pause=500&color=42b983&center=true&vCenter=true&width=600&lines=%F0%9F%8F%A0+ImmoApp+S%C3%A9n%C3%A9gal;%F0%9F%8F%9E%EF%B8%8F+Application+immobili%C3%A8re+moderne;%F0%9F%87%B8%F0%9F%87%B3+Made+in+Senegal;%F0%9F%91%A8%F0%9F%8F%BD%E2%80%8D%F0%9F%92%BB+by+Salif;%F0%9F%94%A5+CI%2FCD+Jenkins+%2B+k3s+%2B+ArgoCD;%F0%9F%90%B3+Docker+%2B+Kubernetes;%F0%9F%92%BE+Vue.js+3+%2B+Pinia;%F0%9F%93%8D+Almadies%2C+Dakar" alt="ImmoApp Sénégal" />
</div>

# 🏠 ImmoApp Sénégal - Application Immobilière

[![Jenkins](https://img.shields.io/badge/Jenkins-CI/CD-blue?logo=jenkins)](https://www.jenkins.io/)
[![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-orange?logo=argo)](https://argo-cd.readthedocs.io/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-k3s-blue?logo=kubernetes)](https://k3s.io/)
[![Docker](https://img.shields.io/badge/Docker-Container-blue?logo=docker)](https://www.docker.com/)
[![Vue.js](https://img.shields.io/badge/Vue.js-3.x-green?logo=vue.js)](https://vuejs.org/)

Application web moderne de recherche immobilière dédiée au marché sénégalais. Trouvez, filtrez et sauvegardez vos propriétés préférées en toute simplicité.


## 📋 Table des matières

- [Fonctionnalités](#-fonctionnalités)
- [Architecture CI/CD](#-architecture-cicd)
- [Prérequis](#-prérequis)
- [Installation](#-installation)
- [Déploiement](#-déploiement)
- [Accès aux services](#-accès-aux-services)
- [Commandes utiles](#-commandes-utiles)
- [Structure du projet](#-structure-du-projet)
- [Dépannage](#-dépannage)
- [Contribuer](#-contribuer)

## ✨ Fonctionnalités

### Application
- 🏠 **Catalogue immobilier** : Appartements, villas, terrains, studios
- 🔍 **Recherche avancée** : Filtres par ville, prix, chambres
- ❤️ **Favoris** : Sauvegarde locale des biens préférés
- 📸 **Galerie d'images** : Navigation multi-photos
- 📱 **Responsive** : Adapté à tous les appareils
- 💰 **Devise CFA** : Affichage des prix en Franc CFA

### Infrastructure
- 🔄 **CI/CD automatisé** : Pipeline Jenkins avec GitHub webhook
- 🐳 **Conteneurisation** : Images Docker sur Docker Hub
- ☸️ **Orchestration** : Kubernetes (k3s) pour le déploiement
- 🎨 **GitOps** : ArgoCD pour la gestion des déploiements

## 🏗 Architecture CI/CD


### Flux détaillé

| Étape | Action | Outil |
|-------|--------|-------|
| 1 | Push du code | `git push` |
| 2 | Déclenchement automatique | GitHub Webhook → Jenkins |
| 3 | Build de l'image Docker | Jenkins → Docker |
| 4 | Push sur registry | Jenkins → Docker Hub |
| 5 | Déploiement sur Kubernetes | Jenkins → k3s |
| 6 | Synchronisation GitOps | ArgoCD → k3s |
| 7 | Application en production | k3s → utilisateurs |

## 📋 Prérequis

### Local
- Node.js 20+
- Docker
- Git
- Compte [Docker Hub](https://hub.docker.com/)
- Compte [GitHub](https://github.com/)

### VPS
- Ubuntu 22.04 LTS
- 4GB RAM minimum
- 2 vCPUs
- Accès root

## 🚀 Installation

### 1. Cloner le projet

```bash
git clone https://github.com/sasow/immo-app.git
cd immo-app

2. Installer les dépendances
  npm install

3. Lancer en développement  
  npm run dev

Build local
  docker build -t sasow/immo-app:latest .
  docker run -p 8080:80 sasow/immo-app:latest
  # Accéder à http://localhost:8080

Push sur Docker Hub
  docker login
  docker push sasow/immo-app:latest  
# UNE SEULE COMMANDE sur le VPS

☸️ Déploiement sur Kubernetes

  # Connexion au VPS
  ssh root@84.247.132.116

  # Vérifier les services
  systemctl status k3s
  systemctl status jenkins
  kubectl get nodes

Déploiement manuel
  # Créer le namespace
  kubectl create namespace immo-app

  # Appliquer les manifests
  kubectl apply -f k8s/namespace.yaml
  kubectl apply -f k8s/deployment.yaml
  kubectl apply -f k8s/service.yaml

  # Vérifier
  kubectl get pods -n immo-app
  kubectl get svc -n immo-app

Mise à jour de l'image
  # Mettre à jour l'image
  kubectl set image deployment/immo-app immo-app=sasow/immo-app:latest -n immo-app

  # Voir le statut
  kubectl rollout status deployment/immo-app -n immo-app

4.2 Installer les plugins nécessaires
  Aller dans Manage Jenkins → Manage Plugins

  Installer ces plugins :

  Docker Pipeline

  GitHub Integration

  Kubernetes CLI

4.3 Ajouter les credentials
  Aller dans Manage Jenkins → Manage Credentials → Add Credentials

  Credential ID	Type	Username	Password/Token
  github-credentials	Username with password	Votre username GitHub	Votre token GitHub
  docker-hub-credentials	Username with password	Votre username Docker Hub	Votre mot de passe Docker Hub
Pour générer un token GitHub :

  GitHub → Settings → Developer settings → Personal access tokens

  Generate new token (classic)

  Sélectionner repo et write:packages

  Copier le token

4.4 Créer le pipeline
  New Item → Nom: immo-app-pipeline

  Type: Pipeline

  Pipeline → Definition: Pipeline script from SCM

  SCM: Git

  Repository URL: https://github.com/VOTRE_USERNAME_GITHUB/immo-app.git

  Credentials: github-credentials

  Branches: */main

  Script Path: Jenkinsfile

  Sauvegarder

4.5 Configurer le webhook GitHub (déclenchement automatique)

  # Sur GitHub : votre repo → Settings → Webhooks → Add webhook
  Payload URL: http://84.247.132.116:8080/github-webhook/
  Content type: application/json
  Events: Just the push event
  Active: ✓

Récupération des mots de passe
  # Jenkins
  cat /var/lib/jenkins/secrets/initialAdminPassword

  # ArgoCD
  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d


immo-app/
├── src/                    # Code source Vue.js
├── public/                 # Assets publics
├── k8s/                    # Manifests Kubernetes
│   ├── namespace.yaml
│   ├── deployment.yaml
│   └── service.yaml
├── argocd/                 # Configuration ArgoCD
│   └── application.yaml
├── Dockerfile              # Build de l'image
├── nginx.conf              # Configuration Nginx
├── Jenkinsfile             # Pipeline CI/CD
├── package.json            # Dépendances
└── README.md               # Documentation

Pod en CrashLoopBackOff

  # Voir les logs du pod
  kubectl logs -n immo-app <pod-name>

  # Voir les détails
  kubectl describe pod -n immo-app <pod-name>

  # Supprimer et recréer
  kubectl delete pod -n immo-app <pod-name>

----------

1. VOUS : git push sur GitHub
         ↓
2. GitHub Webhook → Jenkins
         ↓
3. Jenkins :
   - Build l'image Docker
   - Push sur Docker Hub
   - Met à jour k8s/deployment.yaml
   - Push sur GitHub
         ↓
4. ArgoCD (surveille GitHub) :
   - Détecte le changement dans k8s/
   - Sync automatiquement sur k3s
         ↓
5. k3s :
   - Pull la nouvelle image
   - Redéploie l'application
         ↓
6. Application disponible sur http://IP:30080

Deploiement de l'application sur ArgoCD
  kubectl apply -f https://raw.githubusercontent.com/VOTRE_USERNAME_GITHUB/immo-app/main/argocd/application.yaml