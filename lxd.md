
Linux containers with LXD

According to Ubuntu, “LXD (pronounced lex-dee) is the lightervisor, or lightweight container hypervisor.”

Try it out, and you will see why!

This guide is tested with Ubuntu 18.04.
Step 1 – Install LXD

Start by installing the LXD “container hypervisor”.

you@yourserver:~$ sudo apt install lxd

Step 1 – Initialize storage and virtual networking

Before you can launch container you must initialize LXD. For simplicity and ease of use this example will map an ethernet interface directly to the containers. Each container will receive its own virtual eth0 interface with a unique MAC address but still sharing the same actual host interface.

NOTICE: You will need to substitute exxx with the name of your hosts Ethernet interface.

you@yourserver:~$ sudo lxd init
Would you like to use LXD clustering? (yes/no) [default=no]:
Do you want to configure a new storage pool? (yes/no) [default=yes]:
Name of the new storage pool [default=default]:
Name of the storage backend to use (btrfs, dir, lvm) [default=btrfs]:
Create a new BTRFS pool? (yes/no) [default=yes]:
Would you like to use an existing block device? (yes/no) [default=no]:
Size in GB of the new loop device (1GB minimum) [default=15GB]:
Would you like to connect to a MAAS server? (yes/no) [default=no]:
Would you like to create a new local network bridge? (yes/no) [default=yes]: no
Would you like to configure LXD to use an existing bridge or host interface? (yes/no) [default=no]: yes
Name of the existing bridge or host interface: exxx
Would you like LXD to be available over the network? (yes/no) [default=no]:
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
you@yourserver:~$

You should already be a member of the ‘lxd’ unix group, but you should change your current group id to lxd in order for the group rights to be effectuated.

you@yourserver:~$ newgrp lxd

Step 3 – Launch your first container

You are now ready to create and launch container. The example below will create a container running Ubuntu version 18.04 and name it “yourcontainer”. Wait while the container image downloads from the “ubuntu:” remote registry.

you@yourserver:~$ lxc launch ubuntu:18.04 yourcontainer
Creating yourcontainer
Starting yourcontainer

Step 4 – Verify that your container is running

List all your running containers to verify that your newly container is up and running. Assuming that your physical Ethernet interface is connected to a LAN with DHCP you should verify that the virtual eth0 interface has been assigned an IP.

you@yourserver:~$ lxc list
+---------------+---------+----------------------+------+------------+-----------+
| NAME          | STATE   | IPV4                 | IPV6 | TYPE       | SNAPSHOTS |
+---------------+---------+----------------------+------+------------+-----------+
| yourcontainer | RUNNING | 10.0.0.14 (eth0)     |      | PERSISTENT |         0 |
+---------------+---------+----------------------+------+------------+-----------+

Step 5 – Start using your container

You can execute any command inside of the container as a “root” user.

you@yourserver:~$ lxc exec yourcontainer -- ls -la
total 20
drwx------  3 root root 4096 Nov 10 21:57 .
drwxr-xr-x 22 root root 4096 Nov 10 21:56 ..
-rw-r--r--  1 root root 3106 Apr  9 2018  .bashrc
-rw-r--r--  1 root root  148 Aug 17 2015  .profile
drwx------  2 root root 4096 Nov 10 21:57 .ssh

You can “enter the console” simply by executing the shell inside of the container:

you@yourserver:~$ lxc exec yourcontainer -- /bin/bash
root@yourcontainer:~#
