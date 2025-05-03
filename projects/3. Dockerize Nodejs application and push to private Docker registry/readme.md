# Demo Project:
Dockerize Nodejs application and push to private Docker
registry

### Technologies used:
Docker, Node.js, Amazon ECR

### Project Description:
1. Write Dockerfile to build a Docker image for a Nodejs
application
2. Create private Docker registry on AWS (Amazon ECR)
3. Push Docker image to this private repository



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

## Step 2 - Create private Docker registry on AWS (Amazon ECR)

1. An Amazon AWS account activated is required.
2. Search "Elastic Container Registry" and open this service.
3. Create a new repository
4. Open the repository and view the push commands if required.

## Step 3 - Push Docker image to this private repository

1. We need to make sure we have the AWS CLI and it is authenticated
2. We need to build the image: `docker build -t my-app .`
3. We also need to tag the image: `docker tag my-app:latest SAMPLE.dkr.ecr.us-east-2.amazonaws.com/my-app:latest`
4. Finally we push the image: `docker push SAMPLE.dkr.ecr.us-east-2.amazonaws.com/my-app:latest`