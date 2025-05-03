# Demo Project:
Use Docker for local development

### Technologies used:
Docker, Node.js, MongoDB, MongoExpress

### Project Description:
- Create Dockerfile for Nodejs application and build Docker image
- Run Nodejs application in Docker container and connect to
MongoDB database container locally.
- Also run MongoExpress container as a UI of the MongoDB
database.

## Step 1 - Dockerfile creation

```
FROM node:alpine3.21

ENV MONGO_DB_USERNAME=admin \
    MONGO_DB_PWD=password

RUN mkdir -p /home/app

COPY ./app /home/app

WORKDIR /home/app

RUN npm install

CMD ["node", "server.js"]
```
## Step 2 - Build the app image and run the required containers
1. create a network: `docker network create network-name`
2. Run mongo container in the created network: `docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo`
3. Run mongo express container in the created network: `docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password -e ME_CONFIG_MONGODB_SERVER=mongodb --name mongoExpress --net mongo-network mongo-express`
4. Go to `http://localhost:8081/`
5. Create a new DB named `user-account`
6. Open the DB
7. Create a new collection named `users`
8. Build the application image, make sure to use `mongoUrlDockerCompose` connection string: `docker build -t js-app:1.3 .`
9. Run app container: `docker run -d -p 3000:3000  --name js-app --net mongo-network js-app:1.3` 
10. Open the browser and go to `http://localhost:3000/`