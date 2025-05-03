# Demo Project:
Persist data with Docker Volumes

## Technologies used:
Docker, Node.js, MongoDB

### Project Description:
Persist data of a MongoDB container by attaching a Docker volume to it

## Docker Volumes
Docker volumes are used to persist data in the container so that when the container is stopped then it can be started again with the data it had before shutting it down instead of starting the container with a fresh state.
The way how it works is that a folder in the host is mounted into a directory in the virtual file system of docker. When the container writes data to that virtual file system then the data is also written in the container host.

### Types of volumes:
1. Host volumes: We define where on the host file system the reference is made as well as on which container directory. Example `docher run -v /home/mount/data:/var/lib/mysql/data`
2. Anonymous volumes: Only the container directory is specified, docker decides where on the host the info is stored. Example `docher run -v /var/lib/mysql/data`
3. Named volumes: It specifies the name of the folder on the host file system and the container directory. ( This is the preferred option in production ) Example `docher run -v name:/var/lib/mysql/data`