# Instruction FROM image parentes
FROM centos:8

# Metadata
LABEL maintainer="Pierre <psable@dawan.fr>"

# Instruction RUN => exécuter des commandes Linux

RUN yum install -y java-11-openjdk.x86_64

RUN mkdir /opt/tomcat \
    && curl -O https://downloads.apache.org/tomcat/tomcat-9/v9.0.54/bin/apache-tomcat-9.0.54.tar.gz \
    && tar -zxf apache-tomcat-9.0.54.tar.gz \
    && mv apache-tomcat-9.0.54/* /opt/tomcat/

WORKDIR /opt/tomcat/webapps

RUN curl -O -L https://tomcat.apache.org/tomcat-9.0-doc/appdev/sample/sample.war


EXPOSE 8080

CMD ["/opt/tomcat/bin/catalina.sh", "run"]