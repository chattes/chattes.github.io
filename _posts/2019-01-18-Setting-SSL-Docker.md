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


I used the following 2 containers to get the architecture up and running

[jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)
[JrCs/docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion)

Let me put it in a picture how the solution looks now

[Architecure]:
https://github.com/chattes/chattes.github.io/blob/master/Assets/Cheapp%20Arch.png
"Architecure"

This makes it better as in the future I can add another container and just 
forward request to a new sub domain using the nginx reverse proxy.

The repo is excellently documented and pretty straigh forward to get up and
running.
I had struggled before this with other SSL certbot containers, and they worked
inconsistently for me.


