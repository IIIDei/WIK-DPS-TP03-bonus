# WIK-DPS-TP03 Bonus - Architecture 3 Tiers

Ce bonus déploie une architecture 3 tiers avec :
- **WordPress** répliqué 2 fois (service `wordpress`)
- **MySQL** pour la base de données (service `mysql`)
- **Redis** pour la mise en cache (service `redis`)
- **Nginx** comme reverse-proxy pour servir WordPress (service `reverse-proxy`)

## Prérequis

- Docker et Docker Compose installés.
- Si nécessaire, utilisez `sudo` pour les commandes Docker.

## Lancer l'architecture

1. Clonez ce repository.
   ```bash
   git clone git@github.com:IIIDei/WIK-DPS-TP03-bonus.git
   ```
2. Dans le dossier du projet, lancez :
   ```bash
   sudo docker compose up --build -d
   ```
   Cette commande va construire et lancer tous les services en arrière-plan.

3. Vérifiez que tous les conteneurs sont en cours d'exécution :
   ```bash
   sudo docker compose ps
   ```

## Tester l'accès

Ouvrez votre navigateur ou utilisez curl pour accéder à WordPress via le reverse-proxy :
```bash
curl -X GET -i http://localhost:8080
```
Vous devriez voir la page d'accueil de WordPress (ou être redirigé vers le setup si c'est la première exécution).

## Arrêter l'architecture

Pour arrêter et supprimer les conteneurs et le réseau créé par Docker Compose, exécutez :
```bash
sudo docker compose down
```

## Remarques

- Les variables d'environnement de MySQL et WordPress sont définies dans le fichier `docker-compose.yaml`.
- La configuration de Nginx se trouve dans `nginx.conf` et effectue un load balancing simple vers le service `wordpress`.
- Les données MySQL sont persistées dans le volume `mysql_data`.
