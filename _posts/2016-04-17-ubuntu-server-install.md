---
layout: post
title: How to install Ubuntu Server on VirtualBox vitual machine
description: Install Ubuntu server using Oracle VM Virtualbox 
keywords: Ubuntu Server, Linux, Install, Wily Werewolf, Windows 7, VirtualBox, NAT, port forward
categories: Linux 
---

*This step by step guide explains*

* Basic server intallation 
* Enable networking and internet access using NAT feature
* Enable ssh access from host via port forwarding feature

-----

### Basic server installation

Head to [www.ubuntu.com](http://www.ubuntu.com/download/server), and download the bootable ISO image file (~647MB). Only 64bit version is available so you need to have a system with 64bit architecture.

Create a new **virtual machine** in Oracle VM Virtualbox, the wizard will step through the following

* Name and Operating system: Name: Ubuntu Server Type:Linux Version: Ubuntu (64 bit)
* Memory size: 1024 MB
* Hard disk: Create a virtual hard disk now
* Hard disk file type: VDI
* Storage on a physical hard disk: Dynamically allocated 
* File Location and size: 24 GB

<center>
<img src="{{ site.baseurl }}/public/images/ubuntu-server-install/vmsettings.PNG" width="580px">
</center>
Set the downloaded ISO image in optical drive via Storage -> IDE Controller -> Optical drive.
<center>
<img src="{{ site.baseurl }}/public/images/ubuntu-server-install/optical-drive.PNG" width="580px">
</center>
Start the virtual instance and virtual machine will boot from Optical drive. Use left and right arrow keys to view the steps of installation process

{% include ubuntu-install-carousel.html %}

<br>
Now we have basic server installed. 

### Check the internet access  
Check if we can access internet by doing a ping test 

	selva@vmsvr1-ubuntu:~$ ping www.google.com
	PING www.google.com (216.58.197.68) 56(84) bytes of data.
	64 bytes from maa03s21-in-f4.1e100.net (216.58.197.68): icmp_seq=1 ttl=51 time=46.1 ms
	^C
	--- www.google.com ping statistics ---
	1 packets transmitted, 1 received, 0% packet loss, time 0ms
	rtt min/avg/max/mdev = 46.149/46.149/46.149/0.000 ms
	selva@vmsvr1-ubuntu:~$ ifconfig
	enp0s3    Link encap:Ethernet  HWaddr 08:00:27:cc:5a:d4
	          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
	          inet6 addr: fe80::a00:27ff:fecc:5ad4/64 Scope:Link
	          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
	          RX packets:83 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:215 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:1000
	          RX bytes:9828 (9.8 KB)  TX bytes:40321 (40.3 KB)

	lo        Link encap:Local Loopback
	          inet addr:127.0.0.1  Mask:255.0.0.0
	          inet6 addr: ::1/128 Scope:Host
	          UP LOOPBACK RUNNING  MTU:65536  Metric:1
	          RX packets:304 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:304 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:0
	          RX bytes:20480 (20.4 KB)  TX bytes:20480 (20.4 KB)
	selva@vmsvr1-ubuntu:~$

### Default networking
The default networking type is NAT with any newly created virtual machine. It works out of the box and does need any configuration. When a new virtual machine is created, an virtual ethernet adopter is attached and NAT is enabled by default.

<center>
<img src="{{ site.baseurl }}/public/images/ubuntu-server-install/networking-nat.PNG">
</center>

* Guest can access outside network including host
* Outside computers including host can’t access guest

To enable access to specific services of guest system, from outside network including host system we need to setup port forwarding. As an example let's enable SSH access to guest from host system.

Check the open ssh server installed in the guest system and running, if not install it
	
	selva@vmsvr1-ubuntu:~$ ps -eaf | grep sshd
	selva     1169  1155  0 23:33 tty1    00:00:00 grep --color=auto sshd
	//SSH not running let's install it

	selva@vmsvr1-ubuntu:~$ sudo apt-get install openssh-server openssh-client

From the virutal machine settings, select network and in Adapter1, add forwarding port 2222 from host system to port 22 on the guest system.

<center>
<img src="{{ site.baseurl }}/public/images/ubuntu-server-install/nat-networking.PNG" width="480px">
</center>
<center>
<img src="{{ site.baseurl }}/public/images/ubuntu-server-install/nat-port-forward.PNG" width="280px">
</center>

Now we can use putty or a bash shell to connect 

	$ ssh -p2222 selva@localhost
	The authenticity of host '[localhost]:2222 ([127.0.0.1]:2222)' can't be established.
	ECDSA key fingerprint is SHA256:XHLlo/aBAB9ftvx/azT6YyqW7iSD7QlQPUp8az3ONEk.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '[localhost]:2222' (ECDSA) to the list of known hosts.
	selva@localhost's password:
	Welcome to Ubuntu 15.10 (GNU/Linux 4.2.0-16-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	Last login: Mon Apr 18 23:38:00 2016 from 192.168.2.1
	selva@vmsvr1-ubuntu:~$

I hope this tutorial will help installing Ubuntu server for newbies starting with Linux, where a physical Linux box is not availble. A virtual machine in contrast is a great resouce to learn linux and break!