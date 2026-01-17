# 🌸 Iris AI Service - Guide de Déploiement Docker

## 📋 Description du Projet

Application complète de prédiction basée sur le dataset Iris utilisant un modèle RandomForest. Le projet comprend :

- **API FastAPI** : Backend pour les prédictions ML
- **Frontend React** : Interface utilisateur moderne avec Vite
- **Conteneurisation Docker** : Déploiement isolé et reproductible
- **Orchestration Docker Compose** : Gestion multi-services

---

## 🏗️ Architecture du Projet

```
iris-ai-service/
├── api/
│   ├── Dockerfile              # Image Python pour l'API
│   ├── requirements.txt        # Dépendances Python
│   └── app/
│       ├── main.py            # Point d'entrée FastAPI
│       ├── models.py          # Modèles Pydantic
│       ├── db.py              # Gestion base de données
│       └── model/
│           ├── model.joblib   # Modèle ML entraîné
│           └── model_metadata.json
├── frontend/
│   ├── Dockerfile             # Multi-stage: Node + Nginx
│   ├── package.json
│   ├── nginx.conf            # Configuration Nginx
│   └── src/
│       ├── App.jsx
│       └── main.jsx
├── docker-compose.yml         # Orchestration des services
└── README.md
```

---

## 🚀 Guide de Déploiement Complet

### Prérequis

✅ **Logiciels nécessaires :**

- Docker Desktop (Windows/Mac) ou Docker Engine (Linux)
- Docker Compose v2.0+
- Git

✅ **Système :**

- Ports 8000 et 5174 disponibles
- Au moins 2 GB de RAM disponible
- 5 GB d'espace disque

---

## 📦 Étapes de Déploiement

### Étape 1 : Cloner le Projet

```bash
# Cloner depuis GitLab
git clone <votre-url-gitlab>
cd iris-ai-service
```

### Étape 2 : Vérifier la Structure

```bash
# Vérifier que tous les fichiers sont présents
ls -R
```

Vous devez avoir :

- ✅ `api/Dockerfile`
- ✅ `frontend/Dockerfile`
- ✅ `docker-compose.yml`
- ✅ Fichiers de code source

### Étape 3 : Construction et Lancement

```bash
# Construire et lancer tous les services
docker compose up --build

# OU en mode détaché (arrière-plan)
docker compose up --build -d
```

**Temps estimé :** 3-5 minutes (première fois)

### Étape 4 : Vérification du Déploiement

```bash
# Vérifier que les conteneurs tournent
docker compose ps

# Devrait afficher :
# NAME            STATE    PORTS
# iris-api        Up       0.0.0.0:8000->8000/tcp
# iris-frontend   Up       0.0.0.0:5174->80/tcp
```

### Étape 5 : Tester l'Application

Ouvrez votre navigateur :

1. **API Documentation (Swagger)** : http://localhost:8000/docs
2. **API Health Check** : http://localhost:8000/health
3. **Frontend** : http://localhost:5174

---

## 🐳 Détails des Dockerfiles

### `api/Dockerfile`

```dockerfile
# Use Python 3.11 slim base image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY app/ ./app/

# Expose port 8000
EXPOSE 8000

# Launch with uvicorn
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Caractéristiques :**

- 🐍 Base légère : `python:3.11-slim` (~150 MB)
- 📦 Installation optimisée avec `--no-cache-dir`
- 🚀 Serveur ASGI Uvicorn pour hautes performances
- 🔒 Exposition sécurisée sur 0.0.0.0:8000

### `frontend/Dockerfile`

```dockerfile
# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy source code
COPY . .

# Build React application
RUN npm run build

# Production stage
FROM nginx:alpine

# Copy built files from build stage
COPY --from=builder /app/dist /usr/share/nginx/html

# Copy nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port 80
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
```

**Avantages du Multi-stage Build :**

- ⚡ Image finale ultra-légère (~25 MB vs ~300 MB)
- 🔨 Séparation build/runtime
- 🚀 Nginx performant pour fichiers statiques
- 🔒 Pas de dépendances Node.js en production

### `docker-compose.yml`

```yaml
services:
  api:
    build:
      context: ./api
      dockerfile: Dockerfile
    container_name: iris-api
    ports:
      - "8000:8000"
    environment:
      - API_PORT=8000
      - CORS_ORIGINS=http://localhost:5174
    restart: unless-stopped
    networks:
      - iris-network

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: iris-frontend
    ports:
      - "5174:80"
    environment:
      - VITE_API_BASE=http://localhost:8000
    depends_on:
      - api
    restart: unless-stopped
    networks:
      - iris-network

networks:
  iris-network:
    driver: bridge
```

**Configuration :**

- 🔗 Réseau privé `iris-network` pour communication inter-services
- ⚙️ Variables d'environnement configurables
- 🔄 Restart automatique en cas d'erreur
- 📊 Dépendances gérées (`frontend` attend `api`)

---

## 🔧 Commandes Docker Essentielles

### Gestion des Services

```bash
# Démarrer tous les services
docker compose up

# Démarrer en arrière-plan
docker compose up -d

# Arrêter tous les services
docker compose down

# Redémarrer un service spécifique
docker compose restart api

# Reconstruire sans cache
docker compose build --no-cache

# Voir l'état des conteneurs
docker compose ps
```

### Logs et Debugging

```bash
# Voir les logs de tous les services
docker compose logs -f

# Voir les logs d'un service spécifique
docker compose logs -f api

# Accéder au shell du conteneur API
docker compose exec api /bin/bash

# Accéder au shell du conteneur Frontend
docker compose exec frontend /bin/sh

# Voir les ressources utilisées
docker stats
```

### Nettoyage

```bash
# Arrêter et supprimer les conteneurs
docker compose down

# Supprimer aussi les volumes
docker compose down -v

# Nettoyer les images inutilisées
docker image prune -a

# Nettoyage complet du système Docker
docker system prune -a --volumes
```

---

## 🌐 API Endpoints

### 1. Health Check

```http
GET /health
```

**Réponse :**

```json
{
  "status": "ok",
  "model_loaded": true,
  "model_type": "RandomForestClassifier",
  "features": ["sepal_length", "sepal_width", "petal_length", "petal_width"]
}
```

### 2. Prédiction

```http
POST /predict
Content-Type: application/json

{
  "sepal_length": 5.1,
  "sepal_width": 3.5,
  "petal_length": 1.4,
  "petal_width": 0.2
}
```

**Réponse :**

```json
{
  "prediction": "setosa",
  "probabilities": {
    "setosa": 0.95,
    "versicolor": 0.03,
    "virginica": 0.02
  }
}
```

### Test avec curl

```bash
# Windows PowerShell
Invoke-RestMethod -Uri http://localhost:8000/health -Method Get

# Test de prédiction
Invoke-RestMethod -Uri http://localhost:8000/predict -Method Post -ContentType "application/json" -Body '{"sepal_length": 5.1, "sepal_width": 3.5, "petal_length": 1.4, "petal_width": 0.2}'
```

---

## 🔒 Variables d'Environnement

| Variable        | Service  | Description              | Valeur par Défaut     |
| --------------- | -------- | ------------------------ | --------------------- |
| `API_PORT`      | API      | Port d'écoute de l'API   | 8000                  |
| `CORS_ORIGINS`  | API      | Origines CORS autorisées | http://localhost:5174 |
| `VITE_API_BASE` | Frontend | URL de base de l'API     | http://localhost:8000 |

**Modification :**

Éditez `docker-compose.yml` pour changer les valeurs :

```yaml
environment:
  - API_PORT=8080
  - CORS_ORIGINS=http://localhost:3000,http://example.com
```

---

## 🧪 Tests

### Tester l'API avec Python

```python
import requests

# Health check
response = requests.get("http://localhost:8000/health")
print(response.json())

# Prédiction
data = {
    "sepal_length": 5.1,
    "sepal_width": 3.5,
    "petal_length": 1.4,
    "petal_width": 0.2
}
response = requests.post("http://localhost:8000/predict", json=data)
print(response.json())
```

### Tests Unitaires

```bash
# Depuis le conteneur
docker compose exec api pytest tests/test_api.py

# Localement
cd api
pytest tests/test_api.py -v
```

---

## 🐛 Troubleshooting

### Problème 1 : Port déjà utilisé

**Erreur :**

```
Error: bind: address already in use
```

**Solution :**

```bash
# Windows - Trouver le processus
netstat -ano | findstr :8000

# Tuer le processus (remplacer PID)
taskkill /PID <PID> /F

# OU changer le port dans docker-compose.yml
ports:
  - "8001:8000"
```

### Problème 2 : Le frontend ne communique pas avec l'API

**Symptômes :**

- Erreur CORS
- Erreur de connexion

**Solutions :**

1. Vérifier que `VITE_API_BASE` est correct
2. Vérifier que `CORS_ORIGINS` inclut l'URL du frontend
3. Vérifier que les deux services sont sur le même réseau
4. Rebuild le frontend après changement de variable :
   ```bash
   docker compose up --build frontend
   ```

### Problème 3 : Erreur de build

**Erreur :**

```
ERROR: failed to solve: process "/bin/sh -c npm install" did not complete
```

**Solution :**

```bash
# Nettoyer le cache Docker
docker compose build --no-cache

# Supprimer toutes les images
docker compose down --rmi all
docker compose up --build
```

### Problème 4 : Conteneur s'arrête immédiatement

```bash
# Voir les logs
docker compose logs api

# Vérifier les erreurs de dépendances
docker compose exec api pip list
```

### Problème 5 : Modèle non trouvé

**Erreur :**

```
FileNotFoundError: model.joblib not found
```

**Solution :**

```bash
# Vérifier que le modèle existe
ls api/app/model/

# Reconstruire l'image
docker compose build api
```

---

## 📊 Monitoring (Optionnel)

Configuration Prometheus/Grafana disponible dans `monitoring/` :

```bash
# Ajouter au docker-compose.yml
services:
  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
```

**Accès :**

- Prometheus : http://localhost:9090
- Grafana : http://localhost:3000 (admin/admin)

---

## 🔐 Sécurité - Bonnes Pratiques

### En Production

1. **Ne pas exposer les ports directement**

   ```yaml
   # Utiliser un reverse proxy (nginx, traefik)
   expose:
     - "8000"
   ```

2. **Utiliser des secrets**

   ```yaml
   secrets:
     db_password:
       file: ./secrets/db_password.txt
   ```

3. **Limiter les ressources**

   ```yaml
   deploy:
     resources:
       limits:
         cpus: "0.50"
         memory: 512M
   ```

4. **Scanner les vulnérabilités**
   ```bash
   docker scan iris-api:latest
   ```


### Structure du Repository

```
iris-ai-service/
├── README.md                  # Documentation principale
├── README-DEPLOY.md          # Ce guide de déploiement
├── .gitignore
├── docker-compose.yml
├── api/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app/
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   └── src/
└── monitoring/
    ├── prometheus.yml
    └── grafana-fastapi-dashboard.json
```


## 📚 Ressources et Documentation

### Documentation Officielle

- **FastAPI** : https://fastapi.tiangolo.com/
- **Docker** : https://docs.docker.com/
- **Docker Compose** : https://docs.docker.com/compose/
- **React + Vite** : https://vitejs.dev/
- **Nginx** : https://nginx.org/en/docs/

### Tutoriels Recommandés

- [Docker Multi-stage Builds](https://docs.docker.com/build/building/multi-stage/)
- [FastAPI with Docker](https://fastapi.tiangolo.com/deployment/docker/)
- [Nginx Configuration Best Practices](https://www.nginx.com/blog/nginx-configuration-best-practices/)

---


**Bon déploiement ! 🐳**
