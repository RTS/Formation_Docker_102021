# TP Dockerfile TOMCAT

**Exercice 1 : Sample tomcat war**
Le but de ce TP est de construire un Dockerfile qui permettra de generer une image docker tomcat avec une application war intégrée
1. Creer un répertoire de projet TP_Dockerfile/tomcat
2. Dans ce répertoire projet créer un fichier Dockerfile
3. Voici le schéma directeur à suivre :
    * Partir d'une image de base : centos:8
    * Préciser des labels (idéal pour donner des informations lors d'un docker image inspect)
    * Installer le package java-11-openjdk.x86_64 (via yum)
    
        ```
        yum install -y java-11-openjdk.x86_64
        ```

    * Créer un répertoire /opt/tomcat
  
        ```
        mkdir /opt/tomcat
        ```

    * Récupérer un fichier distant : https://downloads.apache.org/tomcat/tomcat-8/v8.5.71/bin/apache-tomcat-8.5.71.tar.gz
  
        ```
        curl -O https://downloads.apache.org/tomcat/tomcat-8/v8.5.71/bin/apache-tomcat-8.5.71.tar.gz
        ```

    * Décompresser l'archive puis déplacer tous le contenu de apache-tomcat-8.5.71 dans /opt/tomcat/
  
        ```
        # tar -zxf apache-tomcat-8.5.71.tar.gz
        # mv apache-tomcat-8.5.71/* /opt/tomcat/
        ```

   
    
    * Utiliser la directive **WORKDIR** pour se positionner dans le répertoire "/opt/tomcat/webapps"
    * Récuperer le fichier distant https://tomcat.apache.org/tomcat-8.0-doc/appdev/sample/sample.war
  
        ```
        curl -O -L https://tomcat.apache.org/tomcat-8.0-doc/appdev/sample/sample.war
        ```

    * Exposer (rendre visible) le port par défaut tomcat 8080
    * Préciser la commande finale à exécuter : 

       ```bash
      CMD ["/opt/tomcat/bin/catalina.sh", "run"]
      ```

----

1. Construire une image nommée  tomcat:8.5.71 à partir de ce Dockerfile
   
   ```docker image build -t {repo}:{tag} .```

2. Démarrer un nouveau conteneur à partir de cette image :
    * Il faut publier un port !!! (pour acceder au conteneur depuis le docker hôte)
    * Il faudra sans doute le démarrer en mode "détaché"
    * Idéalement on lui donne un nom pour le manager plus facilement
  
  
3. Tester l'accès depuis un navigateur sur le docker hôte
> http://{ip_docker_hote}:8080/sample