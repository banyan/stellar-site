---
layout: post
title: MaglicaInternalCloudTool
date: 2011-08-18 17:25:28 +0900
---


# Overview

[Maglica](https://github.com/mizzy/maglica) is a Python library and command-line tool for an internal cloud. (This tool is very primitive beta now.)

This is very simple and based on libvirt, virt-clone and zeromq (for RPC).

The name "Maglica" is derived from a Bosnian word that means "Nebula."

I'm developing this tool because other internal cloud tools(CloudStack, OpenStack, Eucalyptus and so on) are so complicated for internal use.I need a simple tool.

----

# Architecture of Maglica

Maglica has four components.

1. Library
1. CLI 
1. Client worker
1. Host workers

Maglica library is a main component of Maglica.Maglica CLI calls the library
to do the jobs.The library talks to libvirtd on other hosts directly to get
virtual machines information.

Maglica client worker is running on the same host on which CLI runs.
Client worker binds zeromq PUB socket to tcp://*:5555 and REP socket
to tcp://*:5556.

Client worker recieves requests from library through REP socket and throws
request to host workers through PUB socket.Also REP socket recives job results
from host workers.

Host workers are running on hosts which virtual machines are running on.

Host workers connect to the PUB socket of a client worker to receive job
requests and the REP socket of a client worker to throw job results.

[[Image(http://mizzy.org/img/maglica_architecture.jpg)]]

----
# How to use Maglica

You must setup libvirtd running on host machines and accessible through
tcp port. (See http://libvirt.org/remote.html)

Clone Maglica on a host machine and run the host worker.

	
	# git clone git://github.com/mizzy/maglica.git
	# cd maglica
	# cp etc/maglica.conf.example etc/maglica.conf
	

Edit etc/maglica.conf that points to the correct client worker host and port.

	
	# ./scripts/maglica_host_worker
	

Clone Maglica on a client machine.

	
	# git clone git://github.com/mizzy/maglica.git
	# cd maglica
	# cp etc/maglica.conf.example etc/maglica.conf
	

Edit etc/maglica.conf that points to host machines.

Run the client worker.

	
	# ./scripts/maglica_client_worker
	

Run maglica command to list up "original images"(which name is end with
the string "oroginal").

	
	# PYTHONPATH=. ./scripts/maglica image list
	Name                                     Host
	---------------------------------------------------------
	SL6.0-x86_64.original                    host0.example.jp  
	SL6.1-x86_64.original                    host0.example.jp  
	SL6.0-x86_64.original                    host1.example.jp  
	SL6.1-x86_64.original                    host1.example.jp  
	

Run maclica command to clone a virtual machine image.

	
	# ./scripts/maglica vm clone --image=SL6.1-x86_64.original --hostname=vm0.example.jp
	

This command clones the image on a host and rewrites network setting of the image with libguestfs.
