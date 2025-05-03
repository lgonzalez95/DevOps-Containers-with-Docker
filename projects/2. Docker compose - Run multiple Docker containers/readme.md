# Demo Project:
Docker Compose - Run multiple Docker containers

### Technologies used:
Docker, MongoDB, MongoExpress

### Project Description:
Write Docker Compose file to run MongoDB and
MongoExpress containers

```
version: '3'
services:
  mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=${MONGO_INITDB_ROOT_USERNAME}
     - MONGO_INITDB_ROOT_PASSWORD=${MONGO_INITDB_ROOT_PASSWORD}
    volumes:
     - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    restart: always
    ports:
     - 8081:8081
    environment:
     - ME_CONFIG_MONGODB_ADMINUSERNAME=${ME_CONFIG_MONGODB_ADMINUSERNAME}
     - ME_CONFIG_MONGODB_ADMINPASSWORD=${ME_CONFIG_MONGODB_ADMINPASSWORD}
     - ME_CONFIG_MONGODB_SERVER=${ME_CONFIG_MONGODB_SERVER}
    depends_on:
     - "mongodb"
volumes:
  mongo-data:
    driver: local
```