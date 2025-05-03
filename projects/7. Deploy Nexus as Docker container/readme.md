# Demo Project:
Deploy Nexus as Docker container

## Technologies used:
Docker, Nexus, DigitalOcean, Linux

### Project Description:
1. Create and Configure Droplet
2. Set up and run Nexus as a Docker container

## Step 1 - Create and Configure Droplet
1. Create a new droplet
2. Add a firewall, open the ports if required.
3. Login to the server: `ssh root@SERVER_IP`
4. Install docker: 
 - Run `apt update`
 - Run `snap install docker`

## Step 2 - Set up and run Nexus as a Docker container
1. Run the nexus container: 
 - Create the docker volume: `docker volume create --name nexus-data`
 - Create the container: `docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3` 
2. Set insecure docker registry on the server: 
    - Check the current settings: `cat /var/snap/docker/current/config/daemon.json`
    - Edit the file: `vim /var/snap/docker/current/config/daemon.json`
    - Add the line: `"insecure-registries": ["SERVER_IP:PORT"]`
    - Save
3. Restart docker daemon: `systemctl restart snap.docker.dockerd`
4. Login to the self hosted repo to be able to pull images: `docker login -u luis SERVER_IP:PORT`
5. It might be required to rebuild the image to add compatilibility to linux platform, if so run (this is done at the host machine, not the nexus server):
- Build: `docker buildx build --platform linux/amd64,linux/arm64 -t SERVER_IP:PORT/my-app-docker-nexus:5.0 . --load`
- Push the image: `docker push SERVER_IP:PORT/my-app-docker-nexus:5.0`
- Copy the compose file in the server: `% scp docker-compose.yaml USER@SERVERIP:/USER`
- Run the containers: `docker compose up -d`