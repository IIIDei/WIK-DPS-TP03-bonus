Voici le contenu de chaque fichier pour le bonus dans un nouveau repository (par exemple, **WIK-DPS-TP03-bonus**).

---

### 1. docker-compose.yaml

```yaml
version: "3.8"

services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - wp-net

  redis:
    image: redis:alpine
    networks:
      - wp-net

  wordpress:
    image: wordpress:latest
    depends_on:
      - mysql
      - redis
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
      WP_REDIS_HOST: redis
    deploy:
      replicas: 2
      restart_policy:
        condition: any
    networks:
      - wp-net

  reverse-proxy:
    image: nginx:alpine
    depends_on:
      - wordpress
    ports:
      - "8080:80"  # Le reverse-proxy est le seul service exposé sur l'hôte
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    networks:
      - wp-net

networks:
  wp-net:
    driver: bridge

volumes:
  mysql_data:
```

---

### 2. nginx.conf

```nginx
events { }

http {
    upstream wordpress_backend {
        # Le DNS Docker résout "wordpress" vers les conteneurs du service wordpress
        server wordpress:80;
    }

    server {
        listen 80;
        server_name localhost;

        location / {
            proxy_pass http://wordpress_backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

---

### 3. README.md

```markdown
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

```

---

Avec ces fichiers, vous avez tout le nécessaire pour déployer l'architecture bonus. Vous pouvez créer un nouveau repository (ou un dossier dédié) avec cette structure et pousser ces fichiers sur GitHub.