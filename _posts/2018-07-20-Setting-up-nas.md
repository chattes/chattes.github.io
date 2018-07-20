---
layout: post
title: How I setup my home NAS Server
---

#### Things Needed ####
* Raspberry Pi 3
* External HDD
* SD Card --> I used a Class 10 32G SD Card.
* A simple mobile charger to power te pi
* A laptop is required to do initial setup
* An external HDMI capabled monitor or TV
* Internet Connection!


#### Raspberry Pi as NAS Server ####

We will be using the cheap and low powered Raspberry PI as our home NAS Server.

We need to download an image of Raspbian which is the debian based OS that will power our pi.

We can install multiple OS in the PI, but since I am setting it up as a headless
server , without the need for any GUI, we will use the [Rasbian Stretch Lite](https://www.raspberrypi.org/downloads/raspbian/) image.
Download the image into your laptop/desktop(I am using a macbook).

Next we write the Image into the SD Card. We can us the [Etcher](https://etcher.io/) for doing the
same. This will format the SD Card.

Once the image has been burnt to the SD Card. Put the SD Card into the Raspberry
Pi and power it on. We need to connect the Raspberry Pi at this point to a
monitor or TV. This needs to be done only once as later we can SSH into the Pi
from any other laptop.

Also connect an ethernet cable to the PI as although there is a way to connect
the pi directly to a WIFI connection, it did not work for me. Hence I keep
plugging the Raspberry PI with an Ethernet cable.

Now after the initial boot we should be able to log in to the PI server.
The user is generally ```pi``` and the password ```raspberry```.

