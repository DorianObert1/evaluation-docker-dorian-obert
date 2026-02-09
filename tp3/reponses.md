## Exercice 1 : Compose basique

### 1.1 Premier docker-compose.yml

**Fichier `docker-compose.yml` créé avec les services :**
- PostgreSQL (eval-db) avec volume `db-data`
- Adminer (eval-adminer) sur le port 8080

---

### 1.2 Validation

**Commandes :**
```bash
# Lancement de la stack
docker compose up -d

# Vérification des services
docker compose ps
```

**Connexion à Adminer :** http://localhost:8080
- Système : PostgreSQL
- Serveur : db
- Utilisateur : evaluser
- Mot de passe : evalpass
- Base : evaldb

**Questions :**

- **Comment voir les logs des deux services en temps réel ?**
  ```bash
  docker compose logs -f
  ```

- **Comment accéder au CLI PostgreSQL depuis l'extérieur ?**
  ```bash
  docker exec -it eval-db psql -U evaluser -d evaldb
  ```

---

## Exercice 2 : Application multi-tiers

### 2.1 Architecture complète

**Services ajoutés :**
- API Node.js (eval-api) sur le port 3000
- Redis (eval-redis) avec volume `redis-data`

**Fichiers créés dans `api/` :**
 `app.js`, `package.json` et `Dockerfile`

---

### 2.2 Réseau personnalisé

**Réseau `eval-network` configuré en mode bridge**

**Question : Quel est l'avantage d'un réseau personnalisé ?**

ça permet aux conteneurs de communiquer entre eux par leur nom. C'est plus propre que le réseau par défaut et ça isole les services des autres conteneurs.

---

### 2.3 Script d'initialisation DB

**Fichier `init.sql` créé avec :**
- Création de la table `users`
- Insertion de 3 utilisateurs de test

---

## Exercice 3 : Reverse Proxy Nginx

### 3.1 Configuration Nginx

**Fichiers créés dans `nginx/` :**
- `nginx.conf` : Configuration du reverse proxy
- `index.html` : Interface web

**Le service Nginx :**
- Écoute sur le port 8081 (80 était déjà utilisé)
- Redirige `/api` vers l'API Node.js

---

### 3.2 Test complet

**Commandes de test :**
```bash
# Liste des utilisateurs
curl http://localhost:3000/api/users

# Stats (avec cache Redis)
curl http://localhost:3000/api/stats

# Health check
curl http://localhost:3000/health
```

**Questions :**

- **Comment vérifier que Redis met bien en cache les données ?**
  En appelant `/api/stats` deux fois. La première fois le champ `source` vaut `database`, la deuxième fois il vaut `cache`.

- **Que se passe-t-il si vous arrêtez le service Redis ?**
  L'API plante car elle ne peut plus se connecter à Redis.

---

## Exercice 4 : Environnements

### 4.1 Fichier .env

Les variables sont utilisées dans `docker-compose.yml`.

---

### 4.2 Override pour développement

**Fichier `docker-compose.override.yml` créé avec :**
- Port PostgreSQL exposé (5432)
- Volume pour le code source de l'API (hot reload)
- Labels pour identifier l'environnement

---

