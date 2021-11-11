
Virtual Machines on Ubuntu

Notes…

Create a virtual machine named linux-vm1, add 1 virtual cpu, 1024 MB memory and 10 GB disk. Attach a virtual drive based on an ISO file which contains an installer for a debian8 based OS. Configure a virtual graphics card which makes it possible to use a VNC client to connect to the virtual console via port 5901.

sudo virt-install --name=linux-vm1 --vcpus=1 --memory=1024 --cdrom=/mnt/hdd1/iso/mini.iso --disk size=10 --os-variant=debian8 --graphics vnc,password=KeepItSecret!,port=5901,listen=0.0.0.0
