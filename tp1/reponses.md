## Exercice 1 : Premiers pas

### 1.1 Vérification de l'installation

**Commande :**
```bash
docker --version
```

**Résultat :**
Docker version 29.1.2, build 890dcca

---

### 1.2 Téléchargement d'images

**Commandes :**
```bash
docker pull nginx:alpine
docker pull redis:7-alpine
```

**Questions :**

- **Quelle est la taille de chaque image ?**
  - nginx:alpine : 92.6MB
  - redis:7-alpine : 61.9MB

- **Pourquoi utilise-t-on des images `alpine` ?**
  J’utilise les images Alpine car elles sont très légères et donc elles se téléchargent plus vite, prennent moins de place et réduisent les risques de sécurité
---

### 1.3 Liste des images

**Commande :**
```bash
docker images
```

**Questions :**
- **Quelle commande avez-vous utilisée ?** `docker images`
- **Combien d'images sont présentes ?** 28 images

---

## Exercice 2 : Gestion des conteneurs

### 2.1 Lancer un conteneur Nginx

**Commande :**
```bash
docker run -d --name web-eval -p 8080:80 nginx:alpine
```

---

### 2.2 Inspection du conteneur

**Commandes :**
```bash
docker inspect web-eval
```

**Réponses :**
- **Adresse IP :** 172.17.0.2
- **État (status) :** running
- **Date de création :** 2026-02-09T09:12:37.359865546Z

---

### 2.3 Logs et processus

**Afficher les 10 dernières lignes de logs :**
```bash
docker logs --tail 10 web-eval
```

**Affichez les processus en cours d'exécution dans le conteneur :**
```bash
docker top web-eval
```

---

### 2.4 Exécution de commandes

**Commandes :**
```bash
# 1. Ouverture d'un shell interactif
docker exec -it web-eval /bin/sh

# 2. Création du fichier avec mon nom
echo "Dorian Obert" > /tmp/evaluation.txt

# 3. Vérification que le fichier existe
cat /tmp/evaluation.txt

# 4. Quittez le shell
exit
```

---

## Exercice 3 : Cycle de vie

### 3.1 Arrêt et redémarrage

**Commandes :**
```bash
# Arrêt du conteneur
docker stop web-eval

# Vérification qu'il est arrêté
docker ps -a

# Redémarrage du conteneur
docker start web-eval

# Vérification que le fichier existe toujours
docker exec web-eval cat /tmp/evaluation.txt
```

**Question : Le fichier existe-t-il toujours ? Pourquoi ?**
Oui, le fichier est toujours là car arrêter ou redémarrer le conteneur ne supprime pas les données. Elles disparaissent seulement quand on supprime le conteneur avec docker rm.
---

### 3.2 Création d'un conteneur Redis

**Commandes :**
```bash
# Lancement du conteneur Redis
docker run -d --name cache-eval redis:7-alpine

# Connexion au CLI Redis
docker exec -it cache-eval redis-cli

# Dans CLI Redis :
SET evaluation "reussie"
GET evaluation

# Résultat : 
"reussie"
```

---

### 3.3 Gestion multiple

**Commandes :**
```bash
# Lister tous les conteneurs (actifs et inactifs)
docker ps -a

# Arrêter tous les conteneurs en une seule commande
docker stop $(docker ps -q)

# Supprimer tous les conteneurs arrêtés
docker container prune -f
```

**Questions :**
- **Quelles commandes avez-vous utilisées ?**
  - `docker ps -a` pour lister tous les conteneurs
  - `docker stop $(docker ps -q)` pour arrêter tous les conteneurs
  - `docker container prune -f` pour supprimer tous les conteneurs arrêtés
- **Quelle est la différence entre `docker stop` et `docker rm` ?**
  - `docker stop` : Arrête un conteneur en cours d'exécution et peut être redémarré.
  - `docker rm` : Supprime définitivement un conteneur.

---

## Exercice 4 : Volumes et persistance

### 4.1 Création d'un volume

**Commande :**
```bash
docker volume create data-eval
```

---

### 4.2 Utilisation du volume

**Commande :**
```bash
docker run --rm -v data-eval:/data alpine sh -c "echo 'Données persistantes' > /data/persistant.txt"
```

---

### 4.3 Vérification de la persistance

**Commande :**
```bash
docker run --rm -v data-eval:/data alpine cat /data/persistant.txt
```

**Résultat :** `Données persistantes`

**Question : Expliquez pourquoi les données persistent entre les conteneurs.**
Les données persistent car elles sont stockées dans un volume qui est indépendant des conteneurs et même si on supprime un conteneur, le volume lui sera toujours présent car les volumes ne dépendent pas des conteneurs mais l'inverse.
---

## Nettoyage

```bash
# Supprimer tous les conteneurs
docker rm -f

# Supprimer le volume créé
docker volume rm data-eval
```
