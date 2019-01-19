---
layout: post
title: Securing a Production Server with SSL and Docker Containers
---


#### A Micro Services Architecture ####

In my proudction server for cheappr.io I am running multiple containers.
* There is the backend server with a Dockerized NodeJS
* A Container running the POSTGRES database
* Another container running the Redis Instance
* Finally an Nginx WebServer which serves a ReactJS static web

###### My Pain , My Gain!

All these were pretty starigh forward to setup and get running.
But can everything be so easy, I wanted my site to be secure and 
configure with SSL.

I decided to use the excellent [letsencrytpt](https://letsencrypt.org/).
But it was a real pain to setup for me. Mind you I wanted a dockerized 
container running the letsencrypt certbot as well to automatically renew the 
certificates.

I could not and still don't understand it fully but finally got a solution 
which is so elegant! Yippie.

###### How ??

Let me put it in a picture how the solution looks now




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


