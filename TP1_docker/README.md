# TP1 DOCKER

## Database

nano Dockerfile :  
FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

sudo docker build -t noe/database .  
[+] Building 5.4s (5/5) FINISHED   
 
sudo  docker run -p 5432:5432 -d --name database noe/database  
43c68cd2bdaffa2049ff7c5e9c81ba4e7e6259460fc7c6ae6ea90d036bbd46b9

sudo docker run --link database:db -p 8080:8080 adminer  
=> Me donne bien l'interface WEB, la connexion a réussi avec les credentials

Network
-
Pour avoir le network
- sudo docker network create app-network
- sudo docker rm -f database 
- sudo docker run -p 5432:5432 -d --name database --network app-network noe/database
- sudo docker run -p 8080:8080 --network app-network --name adminer adminer  
=> J'ai bien accès à l'interface sur http://[::]:8080/

Variable d'envitonnement
-
Pour avoir le mot de passe avec une variable d'environnement : 
- On change le Dockerfile :  
FROM postgres:14.1-alpine  
ENV POSTGRES_DB=db \\  
    POSTGRES_USER=usr
- On refait le build :  
sudo docker build -t noe/database .
- On re-éxécute le conteneur avec les variables :   
`sudo docker run -p 5432:5432 -d --name database --network app-network -POSTGRES_PASSWORD=pwd noe/database`

Init database :
-
On ajoute ces 2 lignes au Dockerfile :   
COPY scripts/01-CreateScheme.sql  /docker-entrypoint-initdb.d/   
COPY scripts/02-InsertData.sql /docker-entrypoint-initdb.d/

Puis on rebuild puis run.  
=> On voit bien les données sur l'adminer

Volume : 
- 
- On Créé le volume :  
sudo docker volume create pg_data_volume
- On ajoute au Dockerfile : VOLUME /var/lib/postgresql/data
- On indique le volume lorsqu'on run : sudo docker run -p 5432:5432 -d --name database --network app-network -e POSTGRES_PASSWORD=pwd -v pg_data_volume:/var/lib/postgresql/data noe/database


## JAVA

Dockerfile :

FROM openjdk:11-jre-slim

WORKDIR /app

COPY src/Main.class /app/

CMD ["javac", "Main.class"]

puis build : docker run java-hello-world

## WEBSERVER

Création du DockerFile : 
```
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests


FROM amazoncorretto:17
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

ENTRYPOINT java -jar myapp.jar
```

1-2 Why do we need a multistage build? And explain each step of this dockerfile :

Réponse : Pour ne pas avoir à rebuild à chaque fois => On gagne du temps quand on veut simplement run


clone du projet :  
git clone https://github.com/takima-training/simple-api-student.git

application.yml :  
``` 
  datasource:
    url: jdbc:postgresql://database:5432/db
    username: usr
    password: pwd
    driver-class-name: org.postgresql.Driver
```

docker run --network app-network -p 8080:8080 webserver

=> J'ai bien le résultat à l'adresse : http://localhost:8080/departments/IRC/students

## Serveur HTTP

- Création du index.html et du Dockerfile
- docker build -t my-apache2 .
- docker run -dit --name my-site -p 8081:80 my
-apache2

=> J'ai bien ma page html à http://localhost:8081/

Pour récupérer la conf : docker exec my-site cat /usr/local/apache2/c
onf/httpd.conf > my-httpd.conf

Reverse proxy
-
Ajouts de ces lignes dans conf httpd : 
```<IfModule ssl_module>
</IfModule>
<VirtualHost *:80>
ProxyPreserveHost On
ProxyPass / http://webserver:8080/
ProxyPassReverse / http://webserver:8080/
</VirtualHost>
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so
```

Ajout de cette ligne :  
COPY my-httpd.conf /usr/local/apache2/conf/httpd.conf

=> j'ai bien mon résultat api qui s'affiche pour localhost:8081

Why do we need a reverse proxy?

Un reverse proxy agit comme un intermédiaire entre les appareils clients et les serveurs backend, offrant plusieurs avantages :

    Sécurité : Dissimule les détails des serveurs backend, renforçant la sécurité en évitant un accès direct et d'éventuelles exploitations.

    Équilibrage de charge : Répartit le trafic entrant sur plusieurs serveurs backend, assurant une utilisation optimale des ressources et évitant la surcharge.

    Terminaison SSL : Gère le chiffrement SSL/TLS, déchargeant cette tâche des serveurs backend et simplifiant la gestion des certificats.

## Link application

J'ai créé et complété mon docker-compose.yml : 
```version: '3.7'

services:
    backend:
        build:
            context: ./webserver  # Chemin vers le Dockerfile du backend
        networks:
            - app-network
        depends_on:
            - database

    database:
        image: postgres:14.1-alpine  # Image PostgreSQL
        environment:
            POSTGRES_DB: db
            POSTGRES_USER: usr
            POSTGRES_PASSWORD: pwd
        networks:
            - app-network

    httpd:
        build:
            context: ./http_server  # Chemin vers le Dockerfile d'Apache HTTP Server
        ports:
            - "8080:80"  # Mappage du port 80 du conteneur à 8080 sur l'hôte
        networks:
            - app-network
        depends_on:
            - backend

networks:
    app-network:
```
docker-compose up => Tout se lancer bien


