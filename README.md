![](https://github.com/bspwr/broadway-baseimage/workflows/docker%20build/badge.svg)
# Broadway docker baseimage

This is my fork of [m-bers/broadway-baseimage](https://github.com/m-bers/broadway-baseimage).
## Run GTK3 apps in the browser via docker

### What is it? 
broadway: https://developer.gnome.org/gtk3/stable/gtk-broadway.html


### Requirements:
git, docker



### Quickstart: 

    docker run -it -p 8180:80 ghcr.io/bspwr/broadway-baseimage /bin/bash

In the container shell, 

    start
    apt-get update
    # install GTK3 apps and run them here
    ...

Go to http://localhost:8180 in your browser

### Building an image:

Create a Dockerfile, entrypoint script, and any other assets you want (like a docker-compose.yml). In your entrypoint script, make sure you first run the baseimage script before your own commands, as in docker-virt-manager:

    #!/bin/bash
    /usr/local/bin/start

    # Note: No X server or wayland support--only cli and gtk3
    # ↓↓↓ PUT COMAMNDS HERE ↓↓↓
    ...

In your Dockerfile you generally want to do the following:
* Pull from this image which you just built, e.g. `FROM broadway-baseimage`
* Set branding via environment variables
* Install some packages
* Copy your entrypoint script into the container
* Run your entrypoint script

Example (from [docker-virt-manager](https://github.com/bspwr/docker-virt-manager)):

    FROM ghcr.io/bspwr/broadway-baseimage:latest

    ENV FAVICON_URL='https://raw.githubusercontent.com/virt-manager/virt-manager/931936a328d22413bb663e0e21d2f7bb111dbd7c/data/icons/256x256/apps/virt-manager.png'
    ENV APP_TITLE='Virtual Machine Manager'
    ENV CORNER_IMAGE_URL='https://raw.githubusercontent.com/virt-manager/virt-manager/931936a328d22413bb663e0e21d2f7bb111dbd7c/data/icons/256x256/apps/virt-manager.png'
    ENV HOSTS="[]"

    RUN apt-get update
    RUN apt-get install -y --no-install-recommends virt-manager dbus-x11 libglib2.0-bin gir1.2-spiceclientgtk-3.0 ssh 
    RUN apt-get clean && apt-get autoclean && rm -rf /var/lib/apt/lists/*

    RUN mkdir -p /root/.ssh
    RUN echo "Host *\n\tStrictHostKeyChecking no\n" >> /root/.ssh/config

    COPY startapp.sh /usr/local/bin/startapp

    # overwrite this with 'CMD []' in a dependent Dockerfile
    CMD ["/usr/local/bin/startapp"]



