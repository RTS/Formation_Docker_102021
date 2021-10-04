# Utilisation des volumes

Dans ce lab, nous allons illustrer la notion de volume. Nous verrons notamment comment
définir un volume:
 
  - au lancement d’un container en utilisant l’option -v


## Persistance des données dans un container

Nous allons illustrer pourquoi, par défaut, un container ne doit pas être utilisé pour persister
des données.
- En utilisant la commande suivante, lancez un container basé sur l’image nginx:1.18-alpine, en mode détaché, avec un nom web01

    ```bash
    $ sudo docker container run --name web01 -d nginx:1.18-alpine
    ```


- Se connecter dans le conteneur web01 et modifier le fichier /usr/share/nginx/html/index.html

```bash
# sudo docker exec -it web01 sh
# echo "Hello" > /usr/share/nginx/html/index.html
```

Sortez ensuite du container.

```bash
# exit
```


Lors de la création du container, une layer read-write est ajoutée au dessus des layers read-
only de l’image sous jacente. C’est dans cette layer que les changements que nous avons
apportés dans le container ont étés persistés (création du fichier /usr/share/nginx/html/index.html). 

Nous allons voir comment cette layer est accessible depuis la machine hôte (celle sur laquelle tourne le
daemon Docker) et vérifier que nos modifications sont bien présentes.

Utilisez la command inspect pour obtenir le path de la layer du container c1. 

La clé qui nous intéresse est GraphDriver.

```bash
$ sudo docker container inspect web01
```

Nous pouvons scroller dans l’output de la commande suivante jusqu’à la clé GraphDriver ou
bien nous pouvons utiliser le format Go templates et obtenir directement le contenu de la clé.
Lancez la commande suivante pour n'obtenir que le contenu de GraphDriver.

```bash
$ sudo docker container inspect -f "{{ json .GraphDriver }}" web01
```

Vous devriez obtenir un résultat proche de celui ci-dessous.

```json
{
"Data": {
"LowerDir": "/var/lib/docker/overlay2/d0ffe7...1d66-
init/diff:/var/lib/docker/overlay2/acba19...b584/diff",
"MergedDir": "/var/lib/docker/overlay2/d0ffe7...1d66/merged",
"UpperDir": "/var/lib/docker/overlay2/d0ffe7...1d66/diff",
"WorkDir": "/var/lib/docker/overlay2/d0ffe7...1d66/work"
},
"Name": "overlay2"
}
```

Avec la commande suivante, vérifiez que le fichier index.html se trouve dans le répertoire
référencé par UpperDir.

```bash
$ CONTAINER_LAYER_PATH=$(sudo docker container inspect -f "{{ json
.GraphDriver.Data.UpperDir }}" web01| tr -d '"')
$ sudo find $CONTAINER_LAYER_PATH -name index.html
/var/lib/docker/overlay2/d0ffe7...1d66/diff/data/hello.txt
```


Supprimez le container web et vérifiez que le répertoire spécifié par UpperDir n’existe plus.

```bash
$ sudo docker container rm -f web01
$ sudo ls $CONTAINER_LAYER_PATH
ls: cannot access '/var/lib/docker/overlay2/d0ffe7...1d66/diff': No such file or directory
```

Cela montre que les données créées dans le container ne sont pas persistées et sont
supprimées avec le container.

Si on réinstancie un conteneur on repart de l'image de base.


## Utilisation des volumes bind via la CLI

Instancier un conteneur nginx avec l'ajout de l'option -v comme ci-dessous :

```bash
$ sudo docker container run --name www01 -d -p 8080:80 -v /vagrant/html:/usr/share/nginx/html nginx:1.18-alpine
```
> Note: /usr/share/nginx/html est le répertoire servi par défaut par nginx, il contient les fichiers
index.html et 50x.html.

Ouvrez la page web sur le port publié 8080. Que constatez-vous ?

Ajouter un fichier index.html dans le répertoire /vagrant/html avec un contenu

Rechargez la page web. Que constatez-vous ?

## Utilisation des volumes via la CLI

Les commandes relatives aux volumes ont été introduites dans Docker 1.9. Elles permettent
de manager le cycle de vie des volumes de manière très simple.

La commande suivante liste l’ensemble des sous-commandes disponibles.

```bash
$ sudo docker volume --help
```

La commande create permet de créer un nouveau volume. Créez un volume nommé html avec
la commande suivante.

```bash
$ sudo docker volume create --name html
```

Lister les volumes existants et vérifiez que le volume html est présent.

```bash
$ sudo docker volume ls
DRIVER  VOLUME NAME
local    html
```


Comme pour les containers et les images (et d’autres primitives Docker), la commande
inspect permet d’avoir la vue détaillée d’un volume. Inspectez le volume html :

```bash
$ sudo docker volume inspect html
[
{
"Driver": "local",
"Labels": {},
"Mountpoint": "/var/lib/docker/volumes/html/_data","Name": "html",
"Options": {},
"Scope": "local"
}
]
```



La clé Mountpoint définie ici correspond au chemin d’accès de ce volume sur la machine hôte.

Lorsque l’on crée un volume via la CLI, le path contient le nom du volume et non pas un
identifiant comme nous l’avons vu plus haut.

Utilisez la commande suivante pour lancer un container basé sur nginx et monter le volume
html sur le point de montage /usr/share/nginx/html du container. Cette commande publie
également le port 80 du container sur le port 8080 de l'hôte.

> Note: /usr/share/nginx/html est le répertoire servi par défaut par nginx, il contient les fichiers
index.html et 50x.html.

```bash
$ sudo docker run --name www02 -d -p 8081:80 -v html:/usr/share/nginx/html nginx
```


Depuis l’hôte, regardez le contenu du volume html.

```bash
$ sudo ls -al /var/lib/docker/volumes/html/_data
total 16
drwxr-xr-x 2 root root 4096 Jan 10 17:07 .
drwxr-xr-x 3 root root 4096 Jan 10 17:07 ..
-rw-r--r-- 1 root root 494 Dec 25 09:56 50x.html
-rw-r--r-- 1 root root 612 Dec 25 09:56 index.html
```

Le contenu du répertoire /usr/share/nginx/html du container a été copié dans le répertoire
/var/lib/docker/volumes/html/_data de l’hôte.


Accédez à la page d'accueil en lançant un curl sur http://localhost:8080

```bash
$ curl localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
body {
width: 35em;
margin: 0 auto;font-family: Tahoma, Verdana, Arial, sans-serif;
}
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>
<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```


Depuis l’hôte, nous pouvons modifier le fichier index.html.

```bash
$ sudo -i
$ echo hello !! > /var/lib/docker/volumes/html/_data/index.html
$ exit
```


Utilisez une nouvelle fois la commande curl pour vérifier que le container sert le fichier
index.html modifié.

```bash
curl localhost:8080
HELLO !!!
```