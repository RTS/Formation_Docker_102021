# Creation application micro-service docker

Le but de ce TP est d'assimiler les objets docker (image, container, network et volume) afin de déployer une application micro-service composée de technologies nginx, php et mariadb

## Creation du projet

1) Dans votre répertoire formation docker :

    - Créer un nouveau répertoire nommé : TP_Appli_microservice
    - A partir du dépot de formation github : 
        - recopier l'arborescence contenu dans le répertoire TP_Appli_microservice (répertoires et fichiers)

## Préparation network bridge

1) S'assurer que le réseau **mynetwork** soit créé
  - type : bridge

```bash
$ sudo docker network create mynetwork
```

## Création d'un micro service de type php - nginx:

1) Instancier un conteneur myphp, voici les paramètres attendus :

  - name: myphp
  - detach
  - network : mynetwork
  - bind :
      -v /vagrant/TP_Appli_microservice/php:/srv/http
  - image : php:7.3.31-fpm

2) Vérifier le contenu dans le conteneur php

```bash
$ sudo docker container ls
$ sudo docker container logs myphp
$ sudo docker container exec myphp cat /srv/http/index.php
```

3) Créer le conteneur mynginx avec les paramètres suivants :

  - name: mynginx
  - detach
  - network : mynetwork
  - bind :
      -v /vagrant/TP_Appli_microservice/conf/default.conf:/etc/nginx/conf.d/default.conf
  - publication port : {port_docker_hote}:80
  - image : nginx:1.20-alpine

```bash
$ sudo docker container run --name mynginx -p 8085:80 -d --network mynetwork -v /vagrant/TP_Appli_microservice/conf/default.conf:/etc/nginx/conf.d/default.conf nginx:1.20-alpine
```

3) Test navigateur :

> http://ip_docker_hote:{port_docker_hôte}

## Mise à jour d'un micro-service (mise à jour version)

- On demande de passer en version php 7.4

  - Quel procédure est à adopter ? Il faut détruire l'instance conteneur myphp et le réinstancier avec changement de verison d'image

  ```bash
  $ sudo docker container run --name myphp -d --network mynetwork -v /vagrant/TP_Appli_microservice/php/:/srv/http/ php:7.4.24-fpm
  ```


