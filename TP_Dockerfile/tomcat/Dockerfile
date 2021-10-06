# Instruction FROM image parentes
FROM centos:8

# Metadata
LABEL maintainer="Pierre <psable@dawan.fr>"

# Instruction RUN => exécuter des commandes Linux

RUN yum install -y java-11-openjdk.x86_64

RUN mkdir /opt/tomcat \
    && curl -O https://downloads.apache.org/tomcat/tomcat-10/v10.0.12/bin/apache-tomcat-10.0.12.tar.gz \
    && tar -zxf apache-tomcat-10.0.12.tar.gz \
    && mv apache-tomcat-10.0.12/* /opt/tomcat/

WORKDIR /opt/tomcat/webapps

RUN curl -O -L https://tomcat.apache.org/tomcat-9.0-doc/appdev/sample/sample.war


EXPOSE 8080

# ENTRYPOINT : binaire/script exécuté par défaut (peut tout de même être surchargé lors de l'instanciation
# du conteneur avec l'option --entrypoint )
ENTRYPOINT [ "/opt/tomcat/bin/catalina.sh" ]

# Devient l'argument derrière la commande entrypoint (peut être surchargé à l'instanciation du contenur : méthode classique)
CMD [ "run" ]