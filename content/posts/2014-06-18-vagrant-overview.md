---
date: "2014-06-18T13:25:00Z"
tags:
- linux
- cloud
title: Vagrant Overview
---

Vagrant is a layer above virtual machine manager to automate VM operation.
It use seed file `Vagrantfile` and image file `Box` to abstact VM's information.

###Installation
Vagrant need VirtualBox as backend engine(Provider).

VMware is also available but both VMware and provider plugin need be purchased.

Docker is also available as provider but docker itself runs in VM on OSX. Try on PC later.

###Init

	$ vagrant init

This will generate a init `Vagrantfile` for later customize.
The file can also be retrieved from repository.

	$ vagrant init ubuntu/trusty32

###Box
The base box needed by Vagrantfile can be downloaded from repository automatically.
But the download may be slow.
The download target is a `.box` file.
You can get the box file via other way and add it to the gallery.

	$ vagrant box add ubuntu/trusty32 ./trusty32.box

`ubuntu/trusty32` is the id of box and must be identical to the `config.vm.box` property in `Vagrantfile`.
`trusty32.box` is the box file offline. The URL can be got from `vagrant up` stdout.
The boxes files are stored in `~/.vagrant.d/boxes/`.

###Up and SSH and SyncedFolder

	$ vagrant up

This will create a new VM in VBox if not created yet.
Filesystem of the VM is from box file we got before.
Other post operations are performed refer to Vagrantfile.

	$ vagrant ssh

The session will be established automatically.
Under `ubuntu/trusty`, the ssh connected to `127.0.0.1:2222` as `vagrant` with password `vagrant`.
The port `2222` is forwarded from guest os' `22`.

Current directory's files in host will be mapped to `/vagrant` in guest.
Every change will be synced. The path can be customized in Vagrantfile.

###Networking
There're three types network.

- Port Forward

	As the SSH port 22 -> 2222, the port forward is the simplest method.
	Actually at this time, there's a NAT adapter in guest for communication.
	The NAT is always required by Vagrant.

	Additional port forward can be added in Vagrantfile:

		config.vm.network "forwarded_port", guest: 80, host: 8080

- Host-only/Private Network

	When using host-only network, guest's ip is set in Vagrantfile.
	Host's ip will be `.1` in the same subnet.(e.g. 192.168.33.1)

		config.vm.network "private_network", ip: "192.168.33.10"

- Bridge/Public Network

		config.vm.network "public_network"

###Teardown

- Suspend

		$ vagrant suspend

- Poweroff/Halt
	
	Poweroff gracefully will be tried firstly.

		$ vagrant halt

- Destory

	The VM in VBox will be removed just like `vagrant up` never be run.

		$ vagrant destroy

###Provisioning
Provision is the script which automate the deployment.
Support Shell, Chef, Puppet, Docker...

Vagrantfile:

	config.vm.provision "shell",run: "once", path: "init.sh"
	config.vm.provision "shell",run: "always", inline: "agns &"
