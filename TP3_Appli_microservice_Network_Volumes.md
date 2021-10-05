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


## Ajout mariadb


1. Analyser la doc docker hub pour mariadb et télécharger l'image :

> https://hub.docker.com/_/mariadb

```bash
$ sudo docker image pull mariadb:10.5
```

2. Créer le conteneur mybdd avec les paramètres suivants :

  - name: mybdd
  - detach
  - network : mynetwork
  - variable d'environnement : MARIADB_ROOT_PASSWORD=roottoor
  - image : mariadb:10.5

```bash
$ sudo docker container run -d --name mybdd --network mynetwork -e MARIADB_ROOT_PASSWORD=roottoor mariadb:10.5
```

3. Test bdd :

```bash
$ sudo docker container exec -it mybdd bash
root@d8ac4f4ccb4a:/# 
root@d8ac4f4ccb4a:/# 
root@d8ac4f4ccb4a:/# mysql -u root -proottoor
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 10.5.12-MariaDB-1:10.5.12+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.001 sec)

MariaDB [(none)]> exit

# exit
```

4. Analyser les objet de type volume

```bash
$ sudo docker volume ls
```

  - Que contatez-vous ?

       - Des volumes anonymes ont été générés => prévue dans l'image
       - Mal maitrisé

5. Détruire et réinstancier le conteneur mybdd en précisant un volume mybdd vers /var/lib/mysql du conteneur

```bash
$ sudo docker container rm -f mybdd
$ sudo docker container run -d --name mybdd --network mynetwork -e MARIADB_ROOT_PASSWORD=roottoor -v mybdd:/var/lib/mysql mariadb:10.5
$ sudo docker volume ls
$ sudo docker volume inspect mybdd
$ sudo ls -l /var/lib/docker/volumes/mybdd/_data
```

6. Valider le fonctionnement et le volume


## Modification code php pour jointure vers mybdd

1. Ajouter le contenu du code php dans index.php

  - Tester l'URL

2. Il manque les extensions pdo pdo_mysql dans le conteneur myphp

  - Que pouvons nous faire pour debugger rapidement ?

    - Se connecter dans le conteneur myphp pour essayer d'ajouter l'extension :

      ```bash
      $ sudo docker container exec -it myphp bash
      # docker-php-ext-install pdo pdo_mysql
      ```

    - Il est nécessaire de restart le process php : 

        - Comment ? On restart le conteneur

        ```bash
        $ sudo docker container restart myphp
        ```





