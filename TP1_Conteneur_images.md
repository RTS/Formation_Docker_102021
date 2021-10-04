 TP Docker : Les commandes de base

**Exercice 1 : Hello from Alpine**
Le but de ce premier exercice est de lancer des containers basés sur l'image alpine
1. Instancier un container basé sur alpine en lui fournissant la commande echo hello
2. Quelles sont les étapes effectuées par le docker daemon ?
3. Lancez un container basé sur alpine sans lui spécifier de commande. Qu’observez-vous ?

**Correstion exercice 1**

1. ```$ sudo docker container run alpine echo hello```
2. - Téléchargement de l'image alpine:latest si inexistante localement, création + démarrage d'un conteneur basé sur l'image alpine et exécution de la commande echo. La commande rend la main le conteneur s'éteint
3.  ```$ sudo docker container run alpine```
   - On laisse le comportement par défaut prévu => une image alpine demande au conteneur d'executer sh.

---

**Exercice 2 : shell intéractif**
Le but de cet exercice est lancer des containers en mode intéractif
1. Instancier un container basé sur alpine en mode interactif sans lui spécifier de commande
2. Que s’est-il passé ?
3. Quelle est la commande par défaut d’un container basé sur alpine ?
4. Naviguez dans le système de fichiers
5. Utilisez le gestionnaire de package d’alpine (apk) pour ajouter un package
```
$ apk update
$ apk add curl
```

**Correstion exercice 2**

1. ```$ sudo docker container run -it alpine```
2. Le conteneur est instancier et avec l'option nous sommes connectés dans le conteneur
3. /bin/sh
4. On peut utiliser le conteneur de la même facon qu'un vm Linux alpine, la seule restriction est la dispo des binaire de l'image (commandes)
5. commande apk : on peut ajouter du contenu (répertoires, package dans un conteneur)

---

**Exercice 3 : foreground / background**
Le but de cet exercice est de créer des containers en foreground et en background
1. Lancez un container basé sur alpine en lui spécifiant la commande ping 8.8.8.8
2. Arrêter le container avec CTRL-C
Le container est t-il toujours en cours d’exécution ?
Note: vous pouvez utiliser la command docker ps que nous détaillerons dans l'une des
prochaines lectures), et qui permet de lister les containers qui tournent sur la machine.
3. Lancez un container en background, toujours en lui spécifiant la command ping 8.8.8.8
Le container est t-il toujours en cours d’exécution ?

**Correstion exercice3**

1. ```$ sudo docker container run -it alpine```
2. Le conteneur est arrêté car plus de processus à l'intérieur
3. ``$ sudo docker container run -it alpine```

---

**Exercice 4 : liste des containers**
Le but de cet exercice est de montrer les différentes options pour lister les containers du
système
1. Listez les containers en cours d’exécution. Est ce que tous les containers que vous avez créés sont listés ?
2. Utilisez l’option -a pour voir également les containers qui ont été stoppés
3. Utilisez l’option -q pour ne lister que les IDs des containers (en cours d’exécution ou
stoppés)

**Correstion exercice 4**

1. ```$ sudo docker container ls```
   - Seuls les conteneur encore actifs sont listés
2. ```$ sudo docker container ls -a```
3. ```$ sudo docker container ls -qa```

---

**Exercice 5 : exec dans un container**
Le but de cet exercice est de montrer comment lancer un processus dans un container
existant
1. Lancez un container en background, basé sur l’image alpine. Spécifiez la commande
ping 8.8.8.8 et le nom ping avec l’option --name
2. Observez les logs du container en utilisant l’ID retourné par la commande précédente ou bien le nom du container
Quittez la commande de logs avec CTRL-C
3. Lancez un shell sh, en mode interactif, dans le container précédent
4. Listez les processus du container
Qu'observez vous par rapport aux identifiants des processus ?

**Correstion exercice 5**

1. ```$ sudo docker container run -d --name ping alpine ping 8.8.8.8```
2. ```$ sudo docker container logs -f ping```
3. ```$ sudo docker container exec -it ping /bin/sh```
4. Dans un conteneur, aucuns process du noyau linux, uniquement les processus dédiés à l'appli à fournir

---

**Exercice 6 : cleanup**
Le but de cet exercice est de stopper et de supprimer les containers existants
1. Listez tous les containers (actifs et inactifs)
2. Stoppez tous les containers encore actifs en fournissant la liste des IDs à la commande
stop
3. Vérifiez qu’il n’y a plus de containers actifs
4. Listez les containers arrêtés
5. Supprimez tous les containers
6. Vérifiez qu’il n’y a plus de containers

**Correction exercice 6**

1. ```sudo docker container ls -a```
2. ```$ sudo docker container stop $(sudo docker container ls -q)```
3. ```sudo docker container ls```
4. ```sudo docker container ls -a```
5. ```sudo docker container prune```
6. ```sudo docker container ls -a```

---

**Exercice 7 : publication de port**
Le but de cet exercice est de créer un container en exposant un port sur la machine hôte
1. Lancez un container basé sur nginx en détaché et publiez le port 80 du container sur le port 8080 de
l’hôte
2. Vérifiez depuis votre navigateur que la page par défaut de nginx est servie sur
http://localhost:8080
3. Lancez un second container en publiant le même port
Qu’observez-vous ?

**Correction exercice 7**

1. ```$ sudo docker container run -d -p 8080:80 nginx:1.18-alpine```
2. Le service nginx est affiché
3. Un port ne peut être publié sur deux conteneur en même temps. /!\ Un port peut être publié pour un nouveau conteneur alors que ce même port a déjà été utilisé pour un précedent conteneur qui est arrêté. Il y alors un conflit au redémarrage du précendent conteneur
    - Bien maitriser la matrice de port, le référentiel

---

**Exercice 8 : inspection d'un container Le but de cet exercice est l'inspection d’un container**
1. Lancez, en background, un nouveau container basé sur nginx:1.18 en publiant le port 80
du container sur le port 3000 de la machine host.
Notez l'identifiant du container retourné par la commande précédente.
2. Inspectez le container en utilisant son identifiant (inspect)
3. En utilisant le format Go template, récupérez le nom et l’IP du container
4. Manipuler les Go template pour récupérer d'autres information

**Correstion exercice 8**

1. ```$ sudo docker container run -d -p 30000:80 nginx:1.18-alpine```
2. ```sudo docker container inspec {ID/NOM}```
3. ```sudo docker container inspect -f "{{ .NetworkSettings.IPAddress }}" 2```
   ```$ sudo docker container inspect -f "{{ .Name }}" 2```