# Module Description
This repository contains essential Docker concepts, commands, and best practices for container management. It covers the difference between images and containers, key Docker commands, networking, volumes, and Docker Compose usage. Additionally, it includes steps for pushing images to AWS ECR and configuring a Nexus Docker repository. Best practices for optimizing security, performance, and image management are also outlined.

### Container vs Image
A container is a running environment for an image.\
Images have their own version, identified as TAGs.

### Docker Commads
1. Downloand docker image: `docker pull image-name`
2. Check all images: `docker images`
3. Create container using an image: `docker run image-name`
4. Create container in detached mode: `docker run -d image-name`
5. View running containers: `docker ps`
6. View running and stopped containers: `docker ps -a`
7. Restart container: `docker stop container-ID` then execute `docker start container-ID`
8. Bind container port and host port: `docker run -p host-port:container-port`
9. Check container logs: `docker logs container-id`
10. Create container using custom name: `docker run -d -p 6001:6379 --name container-name image-name`
11. Get container terminal: `docker exec -it container-id /bin/bash` or `docker exec -it container-name /bin/bash`
12. Start containers with docker compose: `docker-compose -f file.yaml up`
13. Stop containers with docker compose: `docker-compose -f file.yaml down`
14. Build custom image: `docker build -t image-name:tag .`
15. Delete container: `docker rm ID`
16. Delete image: `docker rmi ID`


### Container port vs host port
- Containers have their own ports, so we can have two containers with the same port opened.
- However, the host cannot use the same port for different applications.
- To access a container port, it is neccessary to bind its port with a host port

### docker run vs docker start
- `docker run` creates a new container using an image
- `docker start` only starts an existing container

### Docker network

- If multiple containers are in the same network they can talk to each other using only the container name.


### Course demo
1. create a network: `docker network create network-name`
2. Run mongo container in the created network: `docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo`
3. Run mongo container in the created network: `docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password -e ME_CONFIG_MONGODB_SERVER=mongodb --name mongoExpress --net mongo-network mongo-express`
4. When containers are created with `docker-compose` they are assigned to default network, that's why we don't specify it in the docker compose file.
5. When we recreate the containers the data is lost because there is no data persistence unless we define volumes.


### Dockerfile
- It is a blueprint for creating docker images.
- Starts with FROM which is the base image
- `COPY` command is execute in the host, `RUN` command on the container.
- `CMD` is the entry point

### AWS ECR
- We have one container repository for each image, meaning images are not put all together in one single place.
- We store the different tags

### Pushing images to AWS ECR
- We need to make sure we have the aws cli and it is authenticated
- We need to build the image: `docker build -t my-app .`
- We also need to tag the image: `docker tag my-app:latest SAMPLE.dkr.ecr.us-east-2.amazonaws.com/my-app:latest`
- Finally we push the image: `docker push SAMPLE.dkr.ecr.us-east-2.amazonaws.com/my-app:latest`

### Download the image using docker compose
- Need to do docker login to authenticate with AWS ECR (already done before, but if done on other machine it is required)

### Docker Volumes
- Used to persist the data
- A directory of the host system is mounted on another directory of the container.
- When data is persisted on the container, it gets replicated on the host.

### Types of volumes:
1. Host volumes: We define where on the host file system the reference is made as well as on which container directory. Example `docher run -v /home/mount/data:/var/lib/mysql/data`
2. Anonymous volumes: Only the container directory is specified, docker decides where on the host the info is stored. Example `docher run -v /var/lib/mysql/data`
3. Named volumes: It specifies the name of the folder on the host file system and the container directory. Example `docher run -v name:/var/lib/mysql/data`

### Configuring a Docker repository in Nexus
1. Make sure the server and nexus are running.
2. Go to Nexus url and create a new repository, select docker hosted.
3. Create a nexus role and assign the privilege `nx-repository-view-docker-docker-hosted-*`
4. Assign the role to a nexus user
5. Edit the docker repository, add a port under the HTTP: field. In our case `8083`
6. Open the port on the server side, in our case we need to change the digital ocean firewall
7. Configure `Nexus Realm`, in nexus security activate `Docker Bearer Token Realm` and save.
8. Finally, we need to configure docker client to allow our insecure docker hosted repository (we don't have HTTPS configured) 
- In the docker application, go to settings
- Go to docker engine
- Add the line `"insecure-registries" : ["http://nexus-ip:docker-auth-port"]`
- Save and restart docker.
9. Now we can publish the images to the created repository
- First lets build the image: `docker build -t my-app-docker-nexus:1.0 .`
- We also need to tag the image: `docker tag my-app-docker-nexus:1.0 144.126.218.34:8083/my-app-docker-nexus:1.0`
- Finally we can push it: `docker push 144.126.218.34:8083/my-app-docker-nexus:1.0`

## Docker Best Practices
1. Always use official docker images as base image
2. Use specific image versions
3. Avoid images based on full-blown operating systems, instead use small-sized official images
4. Optimize caching image layers
5. Use .dockerignore to exclude files and folders
6. Make use of "Multi-Stage Builds"
7. Use the Least Privileged User
8. Scan your image for security vulnerabilities

## Run application on server with docker-compose
1. Set insecure docker registry on the server: 
    - Check the current settings: `cat /var/snap/docker/current/config/daemon.json`
    - Edit the file: `vim /var/snap/docker/current/config/daemon.json`
    - Add the line: `"insecure-registries": ["SERVER_IP:PORT"]`
    - Save
2. Restart docker daemon: `systemctl restart snap.docker.dockerd`
3. Login to the self hosted repo to be able to pull images: `docker login -u luis SERVER_IP:PORT`
4. It might be required to rebuild the image to add compatilibility to linux platform, if so run:
- Build: `docker buildx build --platform linux/amd64,linux/arm64 -t SERVER_IP:PORT/my-app-docker-nexus:5.0 . --load`
- Push the image: `docker push SERVER_IP:PORT/my-app-docker-nexus:5.0`
- Copy the compose file in the server: `% scp docker-compose.yaml USER@SERVERIP:/USER`
- Run the containers: `docker compose up -d`