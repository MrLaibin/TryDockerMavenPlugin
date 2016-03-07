# TryDockerMavenPlugin

### Prerequisites
* Install Docker
* Install Maven
* Start Docker
````
service docker start
````
* Pull down the latest tomcat image
````
docker pull tomcat
````
* Verify tomcat image downloaded

````
root@sid-Lenovo:~# docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
tomcat                      latest              00ca396ee1ca        3 days ago          356.9 MB
hello-world                 latest              690ed74de00f        4 months ago        960 B
````

### Setup Instructions

* Clone the webapp
````
git clone https://github.com/sid-saddala/TryDockerMavenPlugin.git
cd TryDockerMavenPlugin
````

* Build the docker image by executing the below maven command which uses Spotify's maven docker plugin which will use this [dockerfile](../src/main/docker/dockerfile).
````
mvn clean package docker:build
````

* You can see the mavem logs that the image with your app deployed to tomcat is being built

````
[INFO] --- maven-war-plugin:2.1.1:war (default-war) @ TrySpotifyApp ---
[INFO] Packaging webapp
[INFO] Assembling webapp [TrySpotifyApp] in [/home/sid/Documents/workspaces/ideaProj/TrySpotifyApp/target/TrySpotifyApp]
[INFO] Processing war project
[INFO] Copying webapp resources [/home/sid/Documents/workspaces/ideaProj/TrySpotifyApp/src/main/webapp]
[INFO] Webapp assembled in [32 msecs]
[INFO] Building war: /home/sid/Documents/workspaces/ideaProj/TrySpotifyApp/target/TrySpotifyApp.war
[INFO] WEB-INF/web.xml already added, skipping
[INFO] 
[INFO] --- docker-maven-plugin:0.4.2:build (default-cli) @ TrySpotifyApp ---
[INFO] Copying /home/sid/Documents/workspaces/ideaProj/TrySpotifyApp/target/TrySpotifyApp.war -> /home/sid/Documents/workspaces/ideaProj/TrySpotifyApp/target/docker/TrySpotifyApp.war
[INFO] Copying /home/sid/Documents/workspaces/ideaProj/TrySpotifyApp/src/main/docker/dockerfile -> /home/sid/Documents/workspaces/ideaProj/TrySpotifyApp/target/docker/dockerfile
[INFO] Building image sid-saddala/tryspotifyapp
Step 1 : FROM tomcat:latest
 ---> 00ca396ee1ca
Step 2 : COPY TrySpotifyApp.war /usr/local/tomcat/webapps/TrySpotifyApp.war
 ---> 8111f6b9ab56
Removing intermediate container c8fdc69c0aaf
Step 3 : CMD catalina.sh run
 ---> Running in ec3f344462c2
 ---> 6c2d0d42abc6
Removing intermediate container ec3f344462c2
Successfully built 6c2d0d42abc6
[INFO] Built sid-saddala/tryspotifyapp

````

* Check the new image created

````
root@sid-Lenovo:~# docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
sid-saddala/tryspotifyapp   latest              6c2d0d42abc6        2 minutes ago       356.9 MB
tomcat                      latest              00ca396ee1ca        3 days ago          356.9 MB
hello-world                 latest              690ed74de00f        4 months ago        960 B
````

* Now create a container using the new image we created. 

````
docker run -it -p 8888:8080 sid-saddala/tryspotifyapp:latest

root@sid-Lenovo:~# docker ps
CONTAINER ID        IMAGE                              COMMAND             CREATED             STATUS              PORTS                    NAMES
12db3dc29585        sid-saddala/tryspotifyapp:latest   "catalina.sh run"   20 seconds ago      Up 19 seconds       0.0.0.0:8888->8080/tcp   tender_euler
````

* SSH into the new container by using the docker exec command. Change the container name accordingly.

````
root@sid-Lenovo:~# docker exec -i -t tender_euler /bin/bash
root@12db3dc29585:/usr/local/tomcat# cd webapps
root@12db3dc29585:/usr/local/tomcat/webapps# ls -lrt
total 28
drwxr-xr-x  5 root root 4096 Mar  3 11:58 manager
drwxr-xr-x  5 root root 4096 Mar  3 11:58 host-manager
drwxr-xr-x  6 root root 4096 Mar  3 11:58 examples
drwxr-xr-x 14 root root 4096 Mar  3 11:58 docs
drwxr-xr-x  3 root root 4096 Mar  3 11:58 ROOT
-rw-r--r--  1 root root 2360 Mar  7 05:24 TrySpotifyApp.war
drwxr-xr-x  4 root root 4096 Mar  7 05:59 TrySpotifyApp
root@12db3dc29585:/usr/local/tomcat/webapps# 
````

* Test the webapp by hitting the below URL
````
http://localhost:8888/TrySpotifyApp/
````
