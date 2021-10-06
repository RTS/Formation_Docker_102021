# Formation Docker

## Installation

> https://docs.docker.com/get-docker/

> https://docs.docker.com/engine/install/

> https://docs.docker.com/engine/install/ubuntu/

- Installation environnement dev via le script fourni

   ```bash
   $ curl -fsSL https://get.docker.com -o get-docker.sh
   $ sudo sh get-docker.sh
   ```

- Validation :
  - Service Linux systemd : 
     ```bash
     $ sudo systemctl status docker
     ```

  - Configuration par défaut :
   ```bash
   $ docker system info
   ```

  - Configuration perso :
    > https://docs.docker.com/config/daemon/
    - Creer un fichier /etc/docker/daemon.json

  - Client docker :
    ```bash
    $ docker --help
    $ docker version
    $ sudo docker version
    ```

## Utilisation client docker et liste ressources

```bash
$ sudo docker image ls
$ sudo docker network ls
$ sudo docker volume ls
$ sudo network ls
```

## Ressources de type image 

- Docker HUB : reposirtoy d'images publiques (registry publique)

   > https://hub.docker.com/


- Utilisation d'image officielle

  ```bash
  $ sudo docker image pull {NOM_IMAGE}
  ```

  > Par défaut, si l'on ne spécifie pas de tag : docker ajoute le tag ":latest" à l'image

## Ressources de type conteneur

- On instancie un conteneur à partir d'une image (et son tag)

   ```bash
   $ sudo docker container --help
   $ sudo docker container run ubuntu
   ```

   > Par défaut un conteneur s'instancie, exécute la commande prévue par l'image et s'arrête si plus de processus à l'intérieur


   ```
   $ sudo docker container run nginx:1.18-alpine
   ```

   > Un conteneur basé sur image applicative, déclenche un processus actif et donc reste "up"


- Les options sont à ajouter à l'instanciation du conteneur (après pas possible de modfier l'objet, il faudra en recréer un)

    - Ex : mode background et publication d'un port (pour accès depuis l'extérieur)
    ```bash
    $ sudo docker container run -d -P nginx:1.18-alpine
    ```

- Commandes de troubleshooting :

```bash
$ sudo docker container logs {ID/NAME}
$ sudo docker container exec {ID/NAME} COMMANDE
$ sudo docker container exec -it {ID/NAME} "/bin/sh ou /bin/bash"
```


## Ressource de type volume

- Persister les données en dehors du conteneur, car par défaut un conteneur écrit dans une R/W layer qui ne dure que pendant le cycle de vie du conteneur (sera effacée à la destruction du conteneur)

- https://docs.docker.com/storage/volumes/

- Deux type : bind et volume

    - Bind/mount : c'est un répertoire présent sur le docker hote et vu au sens OS
    - Volume     : c'est une ressource créée dans docker et vu par docker comme objet


- On peut créer des volume avec des options : driver (local mais pas que), option :

   > https://docs.docker.com/engine/reference/commandline/volume_create/
   > https://docs.docker.com/engine/extend/legacy_plugins/
   > https://docs.docker.com/engine/extend/


## Ressource de type network

> https://docs.docker.com/network/

```bash
$ sudo docker network ls
$ sudo docker network inspect bridge
```

- Mise en réseau des conteneurs :

   - Historiquement : link /!\ LEGACY : https://docs.docker.com/network/links/
   - User-defined : https://docs.docker.com/network/bridge/


## Logging docker

- Par défaut, docker utilise le drive json-log pour stocker les logs générées par les output des applis des conteneurs

- On peut modifier le logging driver au niveau de la conf globale du docker hôte ou par conteneur à l'instanciation :

   > https://docs.docker.com/config/containers/logging/configure/#configure-the-default-logging-driver
   > https://docs.docker.com/config/containers/logging/configure/#configure-the-logging-driver-for-a-container



## Industrialisation : docker-compose

> https://docs.docker.com/compose/

1. Installer le binaire docker-compose

   > https://docs.docker.com/compose/install/

   - Téléchargement du binaire :

     ```bash
     $ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
     ```

   - Positionnement des droits d'exécution :

     ```bash
     $ sudo chmod +x /usr/local/bin/docker-compose
     ```

   - Test binaire :

     ```bash
     $ docker-compose version
     ```

2. Utilisation : docker-compose file

> https://docs.docker.com/compose/compose-file/

> Voir le TP finale dans TP_Appli_microservice


3. Déclenchement 

```bash
$ sudo docker-compose up
$ sudo docker-compose up -d
$ sudo docker-compose -f chemin/fichier-compose.yaml -d 
```

4. Troubleshooting

```bash
$ sudo docker-compose ps
$ sudo docker-compose logs
$ sudo docker-compose top
```

5. Cycle de vie 

```bash
$ sudo docker-compose stop
$ sudo docker-compose start
$ sudo docker-compose down
$ sudo docker-compose down -v
```

6. Project name

   - Par defaut, docker-compose prefix les nom des ressource par un nom de projet : le répertoire qui contient le docker-compose
   - On peut le modifier :
      ```bash
      $ sudo docker-compose -p appli up -d
      $ sudo docker-compose -p appli ps
      ```

7. Ressources externes

   - Il est possible de préciser au docker-compose qu'il doit utiliser des ressources déjà créées (ne pas créer de nouvelle ressources)

   > CF docker-compose TP_Appli_microservice
   
      ```yaml
      external: true
      ```


## Les images construites par DOCKERFILE : build

> https://docs.docker.com/engine/reference/builder/

- Fichier Dockerfile, on part d'une image de base et on ajoute des instructions
   - cf TP_Dockerfile

- Commande de build :

   ```bash
   $ docker image build -t nom_image:tag .
   ```

- Best practices :

   > https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
   > https://www.docker.com/blog/intro-guide-to-dockerfile-best-practices/


- Projet pour analyse vulnérabilité du contenu des images : trivy

   > https://github.com/aquasecurity/trivy
   > https://hub.docker.com/r/aquasec/trivy/


## Concept de REGISTRY privée (cloud - réseau local)

- On parle de container registry : plusieurs solutions autre que docker

- Solution fournie par docker :
   > https://docs.docker.com/registry/

      - Ex : 
        - 1. déploiement registry docker en local
        ```bash
        $ sudo docker run -d -p 5000:5000 --name registry -v vol_registry:/var/lib/registry  registry:2
        $ curl http://localhost:5000/v2/_catalog
        ```
        - 2. Tag d'une image du docker hote local avec nom DNS de la registry
        ```bash
        $ sudo docker image tag php:7.4-pdo localhost:5000/php:7.4-pdo
        ```
        - 3. Push de l'image avec son tag qui fait référence à la registry
        ```bash
        $ sudo docker image push localhost:5000/php:7.4-pdo
        $ curl http://localhost:5000/v2/_catalog
        ```

- Solution Gitlab : (registry privée avec contrôle d'accès)

   - 1. Login

        ```bash
        $ sudo docker login registry.gitlab.com
        ```

   - 2. TAG de l'image (ou build directement avec le bon tag)
       
       ```bash
       $ sudo docker image tag alpine:latest registry.gitlab.com/pierre.sable/docker_102021/myalpine:latest
       ```

   - 3. Push 

      ```bash
      $ sudo docker image push registry.gitlab.com/pierre.sable/docker_102021/myalpine:latest
      ```


- Solution cloud provier, solution Harbor (cncf)
  - apporte des fonctionnalités de contrôle, de scan, de gestion de comptes....