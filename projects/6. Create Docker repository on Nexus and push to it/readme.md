# Demo Project:
Create Docker repository on Nexus and push to it

## Technologies used:
Docker, Nexus, DigitalOcean, Linux

### Project Description:
1. Create Docker hosted repository on Nexus
2. Create Docker repository role on Nexus
3. Configure Nexus, DigitalOcean Droplet and Docker to be able to push to Docker repository
4. Build and Push Docker image to Docker repository on Nexus


## Step 1 - Create Docker hosted repository on Nexus
1. Make sure the server and nexus are running.
2. Go to Nexus url and click on create new repository.
3. Select docker hosted.
4. Select the required store.
5. Create

## Step 2 - Create Docker repository role on Nexus
1. Create a nexus role and assign the privilege `nx-repository-view-docker-docker-hosted-*`
2. Assign the role to a nexus user

## Step 3 - Configure Nexus, DigitalOcean Droplet and Docker to be able to push to Docker repository
1. Edit the docker osted repository, add a port under the HTTP: field. In our case `8083`
2. Open the same port on the server side, in our case we need to change the digital ocean firewall
3. Go back to nexus
4. Go to Realms
5. Activate `Docker Bearer Token Realm` and save.
6. Finally, we need to configure docker client to allow our insecure docker hosted repository (we don't have HTTPS configured, this is not required if the nexus server has https) 
- In the docker application, go to settings
- Go to docker engine
- Add the line `"insecure-registries" : ["http://nexus-ip:docker-auth-port"]`
- Save and restart docker.

## Step 4 - Build and Push Docker image to Docker repository on Nexus
1. Login to the self hosted repo to be able to pull images: `docker login -u luis SERVER_IP:PORT`
2. Enter the credentials
3. Now we can publish the images to the created repository
- First lets build the image: `docker build -t my-app-docker-nexus:1.0 .`
- We also need to tag the image: `docker tag my-app-docker-nexus:1.0 144.126.218.34:8083/my-app-docker-nexus:1.0`
- Finally we can push it: `docker push 144.126.218.34:8083/my-app-docker-nexus:1.0`
