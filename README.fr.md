# 💬 Discussion : Docker pour les débutants

```text
Dauer:              1h 30min - 2h
Niveau:             Einsteiger (keine Vorkenntnisse notwendig)
Zielgruppe:         Einsteiger, die Docker kennenlernen wollen
Voraussetzungen:    Laptop mit Internetzugang und Docker installiert
Sprache:            Deutsch
Author:             André Lademann <vergissberlin@gmail.com>
```

## Ziel des Talks

À la fin de la présentation, vous montrerez comment utiliser Docker en général. Vous pouvez regrouper des applications dans des images Docker et les exécuter, déboguer et gérer. Si tout se passe bien, vous pouvez enfin télécharger votre propre image Docker sur Docker Hub et la partager avec d'autres.

* * *

## Termes de l'univers Docker

### Docker

Docker est un logiciel qui permet aux applications de s'exécuter dans des conteneurs. Ces conteneurs sont légers et contiennent tout ce dont ils ont besoin pour fonctionner. Ils peuvent fonctionner sur n’importe quel système d’exploitation sur lequel Docker est installé.

#### Image Docker

Une image Docker est un modèle que Docker utilise pour créer des conteneurs. Une image contient tout ce qui est un[Récipient](#docker-container)il fallait courir. Une image peut être constituée d'un ou plusieurs calques. Chaque couche contient un ensemble d'instructions qui sont exécutées lors de la création d'un conteneur. Si une image contient plusieurs calques, le dernier calque est utilisé comme base et les calques précédents sont ajoutés en superposition.

#### Conteneur Docker

Un conteneur est une instance exécutable d'un[Images Docker](#docker-image). Un conteneur est un environnement isolé composé d'une série de couches. Chaque couche contient un ensemble d'instructions qui sont exécutées lors de la création d'un conteneur. Si un conteneur contient plusieurs calques, le dernier calque est utilisé comme base et les calques précédents sont ajoutés en superposition.

#### Fichier Docker

Un Dockerfile est un fichier qui contient des instructions que Docker utilise pour créer un[Image Docker](#docker-image)créer. Un Dockerfile contient un ensemble d'instructions qui sont exécutées lors de la création d'une image. Si un Dockerfile contient plusieurs instructions, la dernière instruction est utilisée comme base et les instructions précédentes sont ajoutées en superposition.

##### Exemple de fichier Docker

```dockerfile
FROM ubuntu:latest
RUN apt-get update && apt-get install -y cowsay
CMD ["cowsay", "Hello World"]
```

### Docker Compose

Docker Compose est un outil qui vous permet de créer des environnements d'application avec plusieurs[Conteneurs Docker](#docker-container)basé sur les définitions définies dans un fichier YAML. Il utilise des définitions de services pour créer des environnements multi-conteneurs entièrement personnalisables.[Réseaux](#docker-netzwerke)et[volumes de données](#docker-volumes)peut partager.

#### Exemple de composition Docker

```yaml
version: '3.7'

services:
  web:
    image: nginx:latest
    ports:
      - "8000:80"
    volumes:
      - .:/code
    depends_on:
      - redis
  redis:
    image: "redis:alpine"
```

### Réseaux Docker

Les réseaux Docker sont un moyen de connecter des conteneurs entre eux. Docker propose trois types de mise en réseau différents :`bridge`,`host`et`none`.`bridge`est le mode par défaut dans lequel Docker connecte les conteneurs entre eux.`host`utilise le réseau hôte de l'hôte sur lequel le conteneur est exécuté.`none`désactive le réseau pour le conteneur.

#### Exemple Docker Compose avec réseau

```yaml
version: '3.1'

services:
  web:
    image: nginx:latest
    ports:
      - "8000:80"
    volumes:
      - .:/code
    depends_on:
      - redis
  redis:
    image: "redis:alpine"
    networks:
      - backend

networks:
    backend:
```

### Volumes Docker

Les volumes Docker sont un moyen de partager des données entre conteneurs. Docker propose deux types de volumes différents :`bind`et`volume`.`bind`lie un répertoire sur l'hôte à un répertoire dans le conteneur.`volume`crée un volume géré par Docker.

#### Exemple de composition Docker avec volume

```yaml
version: '3.1'

services:
  web:
    image: nginx:latest
    ports:
      - "8000:80"
    volumes:
      - .:/code
    depends_on:
      - redis
  redis:
    image: "redis:alpine"
    volumes:
      - redis-data:/data

volumes:
    redis-data:
```

* * *

## Atelier

### Préparation

Vérifiez les éléments suivants pour vous préparer :

-   [ ] Docker installé et en cours d'exécution`docker version`
-   [ ] Docker Compose installé et en cours d'exécution`docker compose version`
-   [ ] Tu es avec[Centre Docker](https://hub.docker.com/)connecté`docker login`
-   [ ] **Facultatif:**Pour ce repo, appuyez sur l'étoile ⭐ dans le coin supérieur droit :D
-   [ ] Forkez ce référentiel sur GitHub et clonez-le sur votre ordinateur

### Tâches

**Le but des tâches**est-ce un[Image Docker](#docker-image)pour en créer un qui exécute une application Web simple. L'application Web doit générer une simple page HTML. Nous allons ensuite démarrer l'image docker localement et voir comment nous pouvons accéder à l'application via le navigateur.

1.  Création d'un fichier Docker
2.  Démarrer un conteneur
3.  Logging des Containers mit`docker logs`
4.  Ouvrir un site Web dans le navigateur
5.  Debugging des Containers mit`docker exec`
6.  Neustart des Containers
7.  Stoppen des Containers
8.  Utilisation des montages de volumes
9.  Publiez votre propre image dans le registre
10. Création d'un fichier Docker Compose
11. Démarrer, arrêter et déboguer une application avec Docker Compose
12. **Prime:**Configuration d'une action GitHub qui crée automatiquement l'image et la place dans le registre

### Tâche 1 - Création d'un fichier Docker

Examinons d’abord une simple image Docker. Pour ce faire, nous créons un répertoire et créons un fichier appelé`Dockerfile`avec le contenu suivant :

```dockerfile
FROM ubuntu:latest
RUN apt-get update && apt-get install -y cowsay
CMD ["cowsay", "Hello World"]
```

### Tâche 2 – Démarrage d'un conteneur

Nous pouvons maintenant commencer à construire l'image. Pour ce faire, nous passons dans le répertoire et exécutons la commande suivante :

```bash
docker build -t workshop .
```

Le`-t`Flag donne un nom à l’image. Le point à la fin de la commande indique que le Dockerfile se trouve dans le répertoire courant.

Nous pouvons maintenant démarrer le conteneur :

```bash
docker run workshop
```

### Tâche 3 – Enregistrer le conteneur avec`docker logs`

Nous pouvons visualiser le journal du conteneur avec la commande`docker logs`afficher. Pour ce faire, nous avons besoin de l'ID du conteneur, que nous utilisons avec la commande`docker ps`peuvent être affichés.

```bash
docker logs <container-id>
```

### Tâche 4 - Ouvrir un site Web dans le navigateur

Pour un site Web, nous avons besoin d'un serveur Web. Pour ce faire, nous créons un nouveau fichier appelé`Dockerfile`avec le contenu suivant :

```dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```

Pour que nous puissions accéder au site Web dans le navigateur, nous créons un nouveau fichier appelé`index.html`avec le contenu suivant :

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Workshop</title>
  </head>
  <body>
    <h1>Workshop</h1>
  </body>
</html>
```

Nous pouvons maintenant créer l'image et démarrer le conteneur :

```bash
docker build -t workshop .
docker run -p -d 8000:80 workshop
```

Le`-p`Flag lie le port 80 du conteneur au port 8000 de l’hôte. Le`-d`Flag démarre le conteneur en arrière-plan.

### Tâche 5 - Débogage du conteneur avec`docker exec`

Nous pouvons visualiser le contenu du conteneur avec la commande`docker exec`afficher. Pour ce faire, nous avons besoin de l'ID du conteneur, que nous utilisons avec la commande`docker ps`peuvent être affichés.

```bash
docker exec -it <container-id> bash
```

### Tâche 6 : Redémarrer le conteneur

Nous pouvons ouvrir le conteneur avec la commande`docker restart`recommencer. Pour ce faire, nous avons besoin de l'ID du conteneur, que nous utilisons avec la commande`docker ps`peuvent être affichés.

```bash
docker restart <container-id>
```

### Tâche 7 - Arrêt du conteneur

Nous pouvons ouvrir le conteneur avec la commande`docker stop`arrêt. Pour ce faire, nous avons besoin de l'ID du conteneur, que nous utilisons avec la commande`docker ps`peuvent être affichés.

```bash
docker stop <container-id>
```

### Tâche 8 : Utilisation des montages de volumes

Nous pouvons ouvrir le conteneur avec la commande`docker run`avec le`-v`Drapeau de départ. Pour ce faire, nous avons besoin du chemin d'accès au répertoire sur l'hôte et du chemin dans le conteneur.

```bash
docker run -v $PWD:/var/www/html workshop
```

Variable de matrice`$PWD`spécifie le chemin d'accès au répertoire actuel.
Le chemin dans le conteneur se trouve après les deux points.

### Tâche 9 - Publiez votre propre image dans le registre

Nous pouvons créer notre propre image en utilisant la commande`docker push`télécharger dans le registre. Pour ce faire, nous avons besoin du nom de l'image et de la balise.

```bash
docker push <image-name>:<tag>
```

La journée est facultative. Si cela n'est pas précisé, sera-t-il`latest`utilisé.

### Tâche 10 - Création d'un fichier Docker Compose

On peut avoir plusieurs conteneurs avec la commande`docker run`commencer. Pour ce faire, nous avons besoin des noms des images et des ports.

```bash
docker run -p 8000:80 workshop
docker run -p 8001:80 workshop
```

Nous pouvons également créer un fichier Docker Compose. Pour ce faire, nous créons un nouveau fichier appelé`docker-compose.yml`avec le contenu suivant :

```yaml
version: '3.7'
services:
  workshop:
    image: workshop
    ports:
      - 8000:80
  workshop2:
    image: workshop
    ports:
      - 8001:80
```

Vous pouvez trouver d'autres documents sur le sujet[ici](Material/Task-Task-10/task-10.md).

### Tâche 11 - Lancer une application à l'aide de Docker Compose

Nous pouvons utiliser l'application avec la commande`docker-compose up`le début.

```bash
docker-compose up -d
```

Le`-d`Flag démarre l'application en arrière-plan comme Docker.

**Voici une liste des commandes les plus importantes :**

```bash
docker-compose up -d # Startet die Anwendung im Hintergrund
docker-compose down # Stoppt die Anwendung
docker-compose ps # Zeigt die laufenden Container an
docker-compose logs # Zeigt die Logs der Container an
docker-compose exec <service> bash # Startet eine Shell im Container
```

### Tâche 12 : Configuration d'une action GitHub

Nous pouvons créer une action GitHub qui sera déclenchée à chaque pression sur le`main`Branch télécharge une nouvelle image dans le registre. Pour ce faire, nous créons un nouveau fichier appelé`.github/workflows/docker.yml`avec le contenu suivant :

```yaml
name: Docker
on:
  push:
    branches:
      - main
jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Build and push
            uses: docker/build-push-action@v2
            with:
            push: true
            tags: workshop
```

L'action GitHub est mise à jour à chaque pression sur le`main`Branche exécutée. Il crée l'image et la télécharge dans le registre.

Vous pouvez en savoir plus sur les actions GitHub[ici](Material/Task-Task-Bonus/task-bonus.md).

* * *

## Conclusion

Dans cette présentation, nous avons examiné les bases de Docker. Nous avons vu comment Docker est utilisé pour créer et gérer des conteneurs.

Que pouvez-vous faire après l’exposé pour vous assurer que quelque chose colle ?

1.  Créer des images Docker pour un projet en cours
2.  Créer un fichier Docker Compose
3.  Pour les nerds : découvrez Docker Swarm et Kubernetes

## Contribuer

Avez-vous des suggestions d'amélioration? Alors n'hésitez pas à créer une pull request ou à écrire quelques lignes[Forum de discussion](https://github.com/vergissberlin/talk-docker/discussions)ou[Gazouillement](https://twitter.com/vergissberlin).
