
<h1 align="center"> Docker & Kubernetes<h1>


<h4 align="center">A walkthrough Docker&Kubernetes crash course with a summary of basic commands <h4>

---

## Resources

 - [Docker&Kubernetes-AraBigData](https://youtu.be/PrusdhS2lmo?si=OaSjOlv7VH2nZwV9)
 - [AraBigData-Github](https://github.com/ahmedsami76/AraBigData)

----
## [Demo Project](https://github.com/LEGO221/Vue-Docker-App)
#### A simple demo project to containerize a front-end application using dockerfile   

----

## Getting Started

```bash
 docker image pull <image name>:<version>
```
 by default if version not specified it would pull the latest version.

---

```bash
 docker container create <argument> <image name> <command>
 docker container start <argument> <container id or name>
```

```bash
 docker container run <argument> <image>
```
 'run' command can make up for three commands (image pull , create and start)
  as it pulls image(if not found locally) , create a new container and start container.

---

 #### To run executable(command) inside a running container 
```bash
 docker exec -it <container> <command>
 docker exec -it nginx bash
```
---
 #### To stop container
```bash
 docker container stop <container id or container name>
```

 #### To exit container without stopping
```bash
 Ctrl+p then ctrl+q  
```
---

#### Listing commands
```bash
 docker image ls 
 docker container ls       #list running containers
 docker container ls --all #list all containers
```
---
## Remove & Delete
```bash
 docker container rm <container id>
 docker image rm <image name>
 docker container rm $(docker container ls -aq) -f    #remove all containers
 docker image rm $(docker image ls -q) -f             #remove all images
```
---
## Images-Deep dive

 #### To pull unofficial image
```bash
  docker image pull <publisher>/<unofficial image name>:<version>
```
---
 #### To rename container and container hostname 
```bash
  docker container run <argument> --name <new container name> -h <new container hostname> <image:version>
  docker container run -it --name alpine -h alp alpine 
```
So alpine linux container shell for example would look like [root@alp] on running instead of [root@2325f9e04787] and container name would be alpine instead of bla-bla names those made up by default.

---
## Images and Container metadata
```bash
 docker image inspect <image name or id>
 docker container inspect <container name or id>
```
---
## Docker Networking

#### Containers port mapping
```bash
 docker container run<argument> -p <host port>:<container port>
 docker container run -d --name webserver -p 80:80 nginx 
```
 This would create a new container named webserver from nginx image with port mapping that container port 80 would redirect to port 80 on host.

---
#### Copy files to containers

```bash
 docker container cp <file path> <container id or name>:<target path>
 docker container cp ./files/file.txt 87e:/tmp/file.txt
```
 This would copy file.txt from host to /tmp/file.txt path on container with id 87e.

---
#### Identifying other containers
```bash
 docker container run --add-host <container name or id>:<container ip> <image>
 docker container run --add-host web:172.17.0.2 ubuntu
```
 -This would give container ubuntu ability to communicate another container named web which has ip 172.17.0.2
 OR
 -from container ubuntu itself you can add web container to known hosts from /etc/hosts

---
 #### List docker networks
 ```bash
  docker network ls
 ```
---
#### Network Types
 
- Bridge:  creates a new adapter to bridge-connect created container
- Host: exposes the whole container to inherit container's host (simulates the host)
- None: creates an isolated container

---
#### create container on network
```bash
 docker container run <argument> --network <network> image
 docker container run -it --network bridge alpine
```
 by default network type is of type bridge in not given.

---
#### Create new network
```bash
 docker network create <network name> -d <network type>
 docker network create mynet -d host
```
#### Create new network with specified subnet mask
```bash
 docker network create --subnet <netmask> <network name>
 docker network create --subnet 10.0.0.0/16 newnet
```

#### To create a new internal network (isolated)
```bash
 docker network create --internal  <network name>
```
####  To connect&disconnect container on specified network 
```bash
 docker network connect <network> <container>
 docker network disconnect <network> <container>
```
#### Additional helpful commands
```bash
ip link show
ip addr show
ifconfig
curl
```
----

## Docker drivers

> docker volumes info can be found on /var/lib/docker/overlay2 and volumes data in /var/lib/docker/volumes

#### Bind mount volume

```bash
 docker container run -it -v <path on host>:<path on container> <image>
 docker container run -it -v /files/pythonfiles:/app/code python
```
#### Create docker volume
```bash
 docker volume create <volume name>
 docker container run -it -v <volume name>:<path on container> <image>
```
```bash
 docker volume create myvol
 docker container run -it -v myvol:/app/code python 
```
----

## Build image

```bash
 docker commit <container name or id> <new image name>
```
 Then you can build a new container with created image 

> New image name better be conventional to be able to push it docker hub or any cloud storage
  Like username/image name : version

#### Demo
```bash
 docker container run -it --name pyflask python bash
 docker commit pyflask DockerUser/pyflask:v1.0
 docker container run -d -p 5000:5000 --name pyf DockerUser/pyflask:v1.0 python /app/hello.py
```
 Now a new container is built with name pyf you can find result from web browser on localhost:5000

> create changes in file /app/hello.py after the first command in Demo to track changes and inheritance of layers  for new image from old created container 
----

## Dockerfile

[Dockerfile cheat sheet](https://kapeli.com/cheat_sheets/Dockerfile.docset/Contents/Resources/Documents/index)

After Dockerfile creation build new image based on Dockerfile instructions

```bash
docker build -t <new image tag> <Dockerfile path>
```
Then you can casually create a container with newly made image.

----
## Image Registries
#### Login to [dockerhub](https://hub.docker.com)

```bash
docker login
```
#### rename (tag) image 

```bash
docker tag <image> <new image tag>
```
> New image tag better be conventional to be able to push it docker hub or any cloud storage
Like username/image name : version

#### upload image to cloud storage
```bash
docker image push <image>
```
---- 

## Docker-compose

after [Docker-compose installation](https://docs.docker.com/compose/install/)
```bash
chmod +x /usr/local/bin/docker-compose
```

#### Build with docker-compose yaml file (docker-compose.yml)

- within context directory of docker-compose.yml file 
 ```bash
 docker-compose up &    #to run in background
 #or in detatched mode
 docker-compose up -d   #to run in detatched mode
 ```
 > it's better to run in background or with detatched mode to avoid that much of logs in your terminal

- if not in context directory of docker-compose yaml file
 ```bash
 docker-compose -f <file_path>
 ```

List containers for a Compose project
```bash
docker-compose ps
docker-compose ps --all #list all containers, running and stopped
```

remove a compose project 
```bash
docker-compose down
```
> this would remove containers and networks of compose project but won't delete images and volumes


---
## Docker Swarm

#### initalize swarm
`get your host ip address with "ifconfig" or whatever based on your os`
```bash
docker swarm init --advertise-addr 'host ip address':2377 --listen-addr 'host ip address':2377
```
#### Show join token
```bash
docker swarm join-token worker   # worker node token
docker swarm join-token manager  # manager node token
```
#### Add nodes to swarm
`on any other machine add a worker or a manager node based on the token`
```bash
docker swarm join --token <token>
```
#### List swarm nodes
```bash
docker node ls
```
#### Create services on swarm
```bash
docker service create <options> <image>
docker service create --name web -p 80:80 --replicas 5 nginx:1.21
```
#### List swarm services
```bash
docker service ls
```
#### List service distribution on nodes
```bash
docker service ps <service>
docker service ps web
```

#### scaling service 

```
docker service scale <service>=<number_of_replicas>
docker service scale  web=3
```
> this would scale service up or down based on its origin set of replicas

#### Update service

```bash
docker service update --image <new_image> <options> <service>
docker service update --image nginx:latest --update-parallelism 2 --update-delay 5s web 
```

#### Delete service
```bash
docker service rm <service>
docker service rm web
```








