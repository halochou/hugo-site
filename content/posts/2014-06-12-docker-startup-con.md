---
date: "2014-06-12T22:40:00Z"
tags:
- linux
- cloud
title: Docker Quick Tour (con.)
---

##Usage(con.)

- Port mapping can be configured while `docker run`

		$ sudo docker run -d -p 5000:5000 training/webapp python app.py

- Or be configured in `Dockerfile`

		# Dockerfile
		EXPOSE 5901

- Mount a Host Directory as a Data Volume

		$ sudo docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py

##Dockerfile
An example dockerfile to dockerize a node.js application.

	# Specific docker server version
	# DOCKER-VERSION 0.3.4

	# Base image, usually retrieve from docker hub.
	FROM    centos:6.4

	# Enable EPEL for Node.js
	RUN     rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
	# Install Node.js and npm
	RUN     yum install -y npm

	# Bundle app source, dockerfile is in `.` together with `.js` code.
	ADD . /src
	# Install app dependencies
	RUN cd /src; npm install

	# Make 8080 can be mapped to host.
	EXPOSE  8080

	# Start the app.
	CMD ["node", "/src/index.js"]

##Network

	Host
	--------------docker0(bridge)-----------
	|			|		|		|
	vethAQI2QT	...		...
	|			|		|		|
	|--------
	|			|		|		|
	eth0			...
	|
	Container0	...

##Summary
- Sandbox under same linux kernel. Similar AIX WPAR.
- Workflow similar to git.
- Image and container similar to openstack image and instance.
- Virtual Environment, not Virtual Machine.
- Faster to start but less hardware separation.
- Still in heavy development.
