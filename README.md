# TP1 – Exercice 3 : Application web Node.js

> **Objectif** : Containeriser une API Express, puis l’optimiser pour réduire la taille de l’image et ajouter un health check.

---

## Structure du projet
- `node-app/package.json` : dépendances (`express`)
- `node-app/server.js` : API avec 4 routes
- `node-app/.dockerignore` : exclut `node_modules`, etc.
- `node-app/Dockerfile` : version initiale + version optimisée (Alpine + multi-stage)


---

##  Étapes réalisées

### 1. Construction de l’image v1.0
```bash
docker build -t node-app:1.0 -f Dockerfile .
```


### 2. Lancement du conteneur
```bash
docker run -d -p 3000:3000 --name node-app-10 node-app:1.0
```

### 3. Tests des routes
- `GET /` → Page d’accueil HTML
- `GET /api/health` → `{"status":"OK",...}`
- `GET /api/info` → Infos sur Node.js
- `GET /api/time` → Horodatage ISO

### 4. Optimisation du Dockerfile
Utilisation de :
- `node:18-alpine` (image légère)
- `npm ci --only=production`
- Nettoyage du cache npm

### 5. Reconstruction en v1.1
```bash
docker build -t node-app:1.1 -f Dockerfile .
```

### 6. Comparaison des tailles
```bash
docker images | grep node-app
``` 
- `node-app:1.0` → ~1.5 GB
- `node-app:1.1` → ~186 MB

→ Réduction de ~93 % grâce à Alpine et à l’optimisation.

### 7. Test du health check
```bash
docker run -d -p 3000:3000 --name node-app-11 node-app:1.1
# Attendre 10s, puis :
docker inspect --format='{{json .State.Health}}' node-app-11 | jq
```
→ Résultat : `"Status": "healthy"`
