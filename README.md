# **Tutoriel Docker**
👋 Bienvenue sur ce tutoriel à la découverte de Docker.
Retrouvez [la vidéo sur la chaîne.](https://www.youtube.com/watch?v=fZZwN_e3LY).

***
## 📖 **Terminologie**

<ins>Image</ins> : *Modèle et dépendances à partir duquel sera créé un ou des containers.*<br>

<ins>Container</ins> : *Processus et toute ses dépendances virtuellement isolé*<br>

<ins>Dockerfile</ins> : *Liste de commandes pour créer une image (voir section Dockerfile).*<br>

<ins>Docker deamon</ins> : *Processus Docker en arrière plan qui gère images et containers.*<br>

<ins>Registry</ins> : *Lieu d'échange et de stockage d'images (exemple : Docker Hub).*

<ins>Bind-Mount</ins> : *Système de persistance des données. Fait pointer un dossier du container vers un fichier ou dossier de l'hôte, vers l'extérieur du container.*


***
## 💻 **Commandes**

### Structure d'une commande
```
docker <catégorie> <commande> <options>
```
### Naviguer
```js
docker 
// Liste les commandes et les familles de commande

docker system info
// Affiche informations au sujet du système

docker image ls 
// Liste les images (ou 'docker images')

docker image history myImage:0.0.0
// Voir les différentes "couches" qui constituent l'image

docker container -a ls 
// Liste les containers, même inactif (ou 'docker ps -a')
```

### Executer
```js
docker pull hello-world
// Télécharge une image depuis docker-hub

docker create hello-world
// Crée un container depuis une image

docker start myContainer
// Démarre un container arrêté

docker run hello-world
// Raccourci 'docker pull' && 'docker create' && 'docker start'

docker exec 
// Execute une commande dans un container

docker run -it --rm ubuntu bash
// Télécharge et lance ubuntu, execute bash interactif, supprime container aprés exécution
// -it = --interactive + --tty
```

### Volume
```js
docker volume create pgdata
// Crée un volume

docker run -v pgdata:/var/lib/postgresql/data postgres
// Fait persister données du container du dossier data dans volume pgdata

docker run --mount type=bind,source=c:/pgdata,target=/var/lib/postgresql/data postgres
// Fait persister données avec bind-mount 
```

### Arrêter / Supprimer
```js
docker container stop myContainer
// Arrête container

docker rm [container id]
// Supprime container

docker rmi [image name]
// Supprime image

docker system prune
// Supprime container, images, réseaux et volume inutilisés
```

***
## 📁 **Dockerfile**
### Etapes
1. Créer fichier `Dockerfile` & `.dockerignore`
1. Décrire étape de création de l'image (utiliser référence)
1. `docker build -t myname/myimage:0.1 .`

### Référence
```dockerfile
FROM ubuntu:18.04
# Image de base

LABEL my.image.version="0.0.1"
# Donner un nom à votre image

RUN npm install
# Exécuter commandes

WORKDIR /usr/src/app
# Crée dossier si n'existe pas, cd dedans

COPY . /app
# Copie fichiers locaux dans container. Source | Destination

VOLUME /data
# Espace de stockage amener à être modifier (mount point)

EXPOSE 80
# Quel port du container doit être ouvert

ENV MY_VAR="Example" 
# Variable d'environnement

ENTRYPOINT ["cmd", "param1", "param2"]
# Commande principale, mode exec, ne démarre pas le shell

CMD ["cmd", "param1", "param2"]
# Commande optionnel, modifiable avec 'docker run image CMD'
```

## 🕸 **docker-compose**
### Etapes
1. Créer fichier `docker-compose.yml`
1. Décrire étapes de création du setup
1. Démarrer l'environnement : `docker-compose up`
1. Le fermer : `docker-compose down`

### Exemple
```yaml

services:
    app:
      build: .
      depends_on:
        - postgres
      environment:
          DATABASE_URL: postgres://user:pass@postgres:5432/db
          NODE_ENV: development
      ports:
        - "3000:3000"
  
    postgres:
      image: postgres:alpine
      environment:
        POSTGRES_PASSWORD: postgres
      volumes:
          - data:/var/lib/postgresql/data
  
volumes:
    data:
```
