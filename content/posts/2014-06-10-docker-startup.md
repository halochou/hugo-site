---
date: "2014-06-10T21:00:00Z"
tags:
- linux
- cloud
title: Docker Quick Tour
---

![](http://www.docker.com/static/img/nav/docker-logo-loggedout.png)

This is a brief record about my one-day trail on `Docker`.

> Docker is an open platform for developing, shipping, and running applications. Docker is designed to deliver your applications faster. With Docker you can separate your applications from your infrastructure AND treat your infrastructure like a managed application. Docker helps you ship code faster, test faster, deploy faster, and shorten the cycle between writing code and running code.

##Architecture

![arch](http://docs.docker.com/article-img/architecture.svg)

##Inside Docker
To understand Docker's internals, you need to know about three components:

- Docker images.

		- Frozen filesystem , read-only.
		- Used to create container by running command inside.
		- Can be generated from `Dockerfile`.
		- But no way to generate `Dockerfile` from image.

- Docker registries.

		- Repository of pre-build images

- Docker containers.

		- Instance of command running in image.
		- Stoped after command exited but not removed.
		- Can be converted to image.

##Usage
- Search images on Docker Hub.

		$ docker search ubuntu:14.04

- Download(Pull) images from Docker Hub.

		$ docker pull ubuntu:14.04

- Run a command in a new container.

		$ docker run ubuntu:14.04 ping www.google.com

- Start a interactive session in a new container.

		$ docker run -i -t ubuntu /bin/bash # the container will be stoped after exited but can be resume
	-t: tty  -i: interactive

- Start a new container as daemon/detached.

		$ docker run -d ubuntu /bin/bash

- List container.

		$ docker ps # show running container
		$ docker ps -l # show latest container including stoped
		$ docker ps -a # show all container

- Save a container to new images

		$ docker commit <containerID> <newImageName>

- Upload image to Docker Hub Registry.

		$ docker push <imageName>
