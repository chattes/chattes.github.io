---
layout: post
title: Playig with Docker
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
version: '3'
services:
  app:
    deploy:
      resources:
        limits:
          memory: 300M
          cpus: '1'
    build: .
    ports:
      - '3000:3000'
    volumes:
      - .:/home/nodejs/app
      - /home/nodejs/app/node_modules
  cassandra-1:
    deploy:
      replicas: 1
      resources:
        limits:
          memory: 190M
        reservations:
          memory: 76M
    hostname: cassandra-1
    image: cassandra:latest
    command: /bin/bash -c "sleep 1 && echo ' -- Pausing to let system catch up ... -->' && /docker-entrypoint.sh cassandra -f"
    environment:
      HEAP_NEWSIZE: 1M
      MAX_HEAP_SIZE: 1024M 
    expose:
      - 7000
      - 7001
      - 7199
      - 9042
      - 9160
    volumes: # uncomment if you desire mounts, also uncomment cluster.sh
      - ./data/cassandra-1:/var/lib/cassandra:rw    
    ```

		With this seetings I am basically running a node server with memory limits
		and limited cpu and a Cassandra DB 




