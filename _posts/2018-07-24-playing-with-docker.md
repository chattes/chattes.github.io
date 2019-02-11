---
layout: post
title: Playing with Docker
---


#### Installation ####
Install from Docker website for Mac

For NodeJS create DockerFile. This will pull in the environment.
The smallest seems to be alpine distroof Linux, NodejS needs to be built on top
of that.

Create a `docker-compose.yml` file . This file has ll the configurations that I
need for my containers.

#### Example ####

I need to run two conatiners

* A node server
* A database server

This is the yml file i took

```
version: '2'
services:
  app:
    restart: always
    build: .
    ports:
      - 3000:3000
    # command: bash -c 'while !</dev/tcp/db/5432; do sleep 1; done; node index.js'
    links:
      - db
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgres://postgres:password@db:5432/flights
      - REDIS_HOST=redis

    volumes:
       - .:/home/nodejs/app
       - /home/nodejs/app/node_modules
  db:
    image: postgres:9.6.2-alpine
    volumes:
      - ./postgresdata=/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=flights
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
  redis: 
    image: redis:alpine
```

With this seetings I am basically running a node server with memory limits
and limited cpu and a Cassandra DB 

#### Docker + Postgres +Node ####

> Connection Problems
[Connection Issues](https://stackoverflow.com/questions/33357567/econnrefused-for-postgres-on-nodejs-with-dockers)

With  this the Docker containers are up and running for NodeJS + PostGres +
redis
Now we need to make it availabe to network...


The node server can be made available by opening port 80 with nginx and mapping
NodeJS as the upstream server.

[Reference](https://medium.com/@joatmon08/using-containers-to-learn-nginx-reverse-proxy-6be8ac75a757)


#### Oh Pupeteer + NodeJS + Ubuntu

Had some problems today running pupeteer in docker image alpine for Node.

Found this [Pupeteer](https://paul.kinlan.me/hosting-puppeteer-in-a-docker-container/)


#####Docker eating up Disk Space

Check with

```
docker sudo df
```

and clean with

```
docker system prune
```

##### Copy Docker Images From Development to Production(Offline)

If we want to copy our images that we have created using docker-compose.yml file
into a remote system like production in offline mode.
Then:

* docker images
* docker save -o <path for generated tar file.tar> <image/s name> 
* scp .tar to remote system
* In remote system `docker load -i <path to image tar file>`



