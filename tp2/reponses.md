# TP2 : Dockerfile et Construction d'Images - Réponses

## Exercice 1 : Dockerfile basique

### 1.1 Application Node.js

**Fichier app.js, package.json et Dockerfile créés dans le dossier `tp2/node-app`**

---

### 1.2 Build et test

**Commandes exécutées :**

```bash
# Construction de l'image
cd tp2/node-app
docker build -t eval-app:v1 .

# Lancement du conteneur sur le port 3001
docker run -d --name node-eval -p 3001:3000 eval-app:v1

# Test de l'application
curl http://localhost:3001
```

**Résultat :**
```json
{"message":"Hello from Docker!","timestamp":"2026-02-09T10:43:11.366Z","hostname":"7447f3ba6b92"}
```

**Taille de l'image :** 180MB

---

**Questions :**

- **Combien de layers l'image contient-elle ?**
  L'image contient **14 layers** au total.

- **Quelle commande permet de voir l'historique des layers ?**
  ```bash
  docker history eval-app:v1
  ```

---

## Exercice 2 : Optimisation du Dockerfile

### 2.1 Cache des dépendances


**Question : Pourquoi cette approche est plus efficace ?**

Cette méthode permet d’utiliser le cache de Docker. On installe d’abord les dépendances avec le package.json, puis on copie le reste du code.
Du coup, si on modifie le code, Docker ne relance pas npm install. Il le refait seulement si package.json change, ce qui rend les rebuilds plus rapides.

---

### 2.2 Multi-stage build

**Fichier : `Dockerfile.multistage`**

**Build avec le Dockerfile multi-stage et le tag :**
```bash
docker build -f Dockerfile.multistage -t eval-app:v2 .
```

**Comparaison des tailles :**
- eval-app:v1 -> 180MB
- eval-app:v2 -> 184MB

---

### 2.3 Utilisateur non-root

**Commandes ajoutées (voir Dockerfile.multistage)**

**Question : Pourquoi est-ce important pour la sécurité ?**

C'est plus sûr de ne pas lancer le conteneur en root, car root a tous les droits.
En cas de faille, un attaquant pourrait tout contrôler.
Comme l'application n'a pas besoin de ces droits, on utilise un utilisateur avec moins de permissions.

---

## Exercice 3 : Arguments et variables

### 3.1 ARG et ENV

**Modification de app.js :** Ajout de `environment: process.env.APP_ENV` dans la réponse JSON

---

### 3.2 Build avec arguments

**Commandes :**
```bash
# Build avec NODE_VERSION=20
docker build --build-arg NODE_VERSION=20 -t eval-app:v3-node20 .

# Lancer avec APP_ENV=development
docker run -d --name node-eval-v3 -p 3001:3000 -e APP_ENV=development eval-app:v3-node20

# Vérification
curl http://localhost:3001
```

**Résultat :**
```json
{"message":"Hello from Docker!","environment":"development","timestamp":"...","hostname":"..."}
```

**Logs du conteneur :**
```
Server running on port 3000 in development mode
```

---

**Questions :**

- **Quelle est la différence entre ARG et ENV ?**
  - `ARG` : Variable disponible uniquement pendant le build
  - `ENV` : Variable disponible quand le conteneur tourne.

- **Comment passer une variable d'environnement au `docker run` ?**
  ```bash
  docker run -e NOM_VARIABLE=valeur image
  # ou
  docker run --env NOM_VARIABLE=valeur image
  # ou depuis un fichier
  docker run --env-file .env image
  ```

---

## Exercice 4 : Application Python

### 4.1 Dockerfile Python

**Fichiers app.py, requirements.txt et Dockerfile créés dans `python-app/`**

---

### 4.2 HEALTHCHECK

**Instruction HEALTHCHECK ajoutée :**
```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD curl -f http://localhost:5000/health || exit 1
```

**Commandes :**
```bash
# Build
docker build -t eval-flask:v1 .

# Lancer le conteneur
docker run -d --name flask-eval -p 5001:5000 eval-flask:v1

# Tester l'application
curl http://localhost:5001
# {"hostname":"...","environment":"production","message":"Hello from Python Docker!"}

curl http://localhost:5001/health
# {"status":"healthy"}

# Vérifier le healthcheck (attendre 30s)
docker ps

# Nettoyage
docker stop flask-eval && docker rm flask-eval
```
