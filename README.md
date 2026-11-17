#TP_M2_MIAGE_IPM_DOCKER_AB

Exercice 3 : Manipulation de base des conteneurs
Étapes :
1. Vérifiez la version de Docker :
 docker --version
 → Affiche la version installée de Docker
 Docker version 29.9.2, build a82e528

2. Listez les images disponibles sur votre machine :
 docker images
 → Montre toutes les images stockées localement

 IMAGE   ID             DISK USAGE   CONTENT SIZE   EXTRA

3. Téléchargez une image depuis Docker Hub :
 docker pull hello-world
 → Télécharge l'image officielle "hello-world".

Using default tag: latest
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete 
d5e71e642bf5: Download complete 
Digest: sha256:c567b231f65614688711a75f31518a064258a51d442117a3e771a3a8fdba78ef
Status: Downloaded newer image for hello-world:latest
docker.io/library/hello-world:latest


4. Exécutez un conteneur à partir de l'image :
 docker run hello-world
 → Lance un conteneur qui affiche un message de bienvenue.

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/



5. Listez les conteneurs en cours d'exécution :
 docker ps
 → Montre les conteneurs actifs.

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

6. Listez tous les conteneurs (actifs et stoppés) :
 docker ps -a
 → Affiche tout l’historique des conteneurs.

CONTAINER ID   IMAGE         COMMAND    CREATED              STATUS                          PORTS     NAMES
567eb1b6ceb4   hello-world   "/hello"   About a minute ago   Exited (0) About a minute ago             elastic_meninsky

7. Supprimez un conteneur :
 docker rm <ID_conteneur>

 567eb1b6ceb4

8. Supprimez une image :
 docker rmi <ID>

 Untagged: hello-world:latest
Deleted: sha256:c567b231f65614688711a75f31518a064258a51d442117a3e771a3a8fdba78ef