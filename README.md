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