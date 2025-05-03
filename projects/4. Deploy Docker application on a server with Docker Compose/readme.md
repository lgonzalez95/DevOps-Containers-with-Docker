# Demo Project:
Deploy Docker application on a server with Docker
Compose

### Technologies used:
Docker, Amazon ECR, Node.js, MongoDB, MongoExpress

### Project Description:
1. Copy Docker-compose file to remote server
2. Login to private Docker registry on remote server to
fetch our app image
3. Start our application container with MongoDB and MongoExpress services using docker compose

## Step 1 - Copy Docker-compose file to remote server
1. Run the command: `scp docker-compose.yaml root@209.38.158.61:/root`

## Step 2 - Login to private Docker registry on remote server to fetch our app image
1. Install AWS CLI:
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
2. Create an access key to authenticate AWS CLI.
- Go to AWS console
- Go to secrutity settings
- Click on create new access key
3. Run `aws configure` in the termina
4. Enter the access key ID and access key secret
5. Enter the other information asked, not all asked data is required.
6. Run the following command to authenticate docker(replace region and account id): `aws ecr get-login-password --region REGION | docker login --username AWS --password-stdin ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com`


## Step 3 - Start our application container with MongoDB and MongoExpress services using docker compose

1. In the server, where the docker-compose file was copied run: `docker-compose up -d`