
Ubuntu package management
Introduction

On an Ubuntu desktop computer you can install software with the “Ubuntu Software Center” (or simply “Software”) and you can keep  your software up to date with “Software Updater”. These and other graphical package management tools (like Synaptic) can be considered as front ends that hides the complexity of the tools which are actually doing the package management in the background.

If you are running Ubuntu as a server or you simply need more advanced package management functions you will need to familiar yourself with the concept of .deb files, the dpkg tools and the apt tools.

Advanced Package Tool (apt): A set of tools used to retrieve software packages from software repositories as well as to install, remove, and provide information about software packages. The apt tool can be seen as a front end to dpkg tools.

Debian Package Management (dpkg): A set of core tools used to install, remove, and provide information about .deb packages. A Debian Package is a specially crafted file (DEB file) with a .deb extensions.

Debian Packages (.deb): The file contains all the necessary information needed to install the software components the package needs.

Practial examples

Notice that  the examples has intentionally omitted parts of the output which are produced when executing the commands.  You will see a lot more verbose output when you run the examples. Parts of the output is also reformatted to fit better in the layout of this page.
Find the name of a package

Use apt to search for packages containing the description “network map”:

you@yourserver:~$ apt search "network map"
Sorting... Done
Full Text Search... Done
nmap/bionic 7.60-1ubuntu5 amd64
  The Network Mapper

Get the description for a package

Use apt to show information about the nmap package.

you@yourserver:~$ apt show nmap
Package: nmap

Description: The Network Mapper
Nmap is a utility for network exploration or security 
auditing. It supports ping scanning (determine which 
hosts are up), many port scanning techniques, version 
detection (determine service protocols---

Install a package

(You need to run super user do in order to do things that only super users should do).
Use apt to install the package named nmap.

you@yourserver:~$ sudo apt install nmap
Reading package lists... Done

Setting up nmap (7.60-1ubuntu5) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...

Update the list of available packages

Run apt update to update the list of  available packages.

you@yourserver:~$ sudo apt update

2 packages can be upgraded. Run 'apt list --upgradable' ...

List packages that can be upgraded

Use apt to list all the upgradable software packages.

you@yourserver:~$ apt list --upgradable
python-apt/bionic-updates 1.6.3 amd64 [upgradable from: 1.6.2]
python3-apt/bionic-updates 1.6.3 amd64 [upgradable from: 1.6.2]

Upgrade software packages

Use apt to upgrade all software packages to the latest version.

you@yourserver:~$ sudo apt upgrade

2 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.

Remove a package

Use apt to remove the package named nmap

you@yourserver:~$ sudo apt remove nmap

The following packages were automatically installed 
 and are no longer required:
libblas3 liblinear3 liblua5.3-0
Use 'sudo apt autoremove' to remove them.

Removing nmap (7.60-1ubuntu5) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...

Completely remove a package

Use apt-get to purge the package named nmap. Purge meaning that any configuration files are deleted too.

you@yourserver:~$ sudo apt-get purge nmap
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  libblas3 liblinear3 liblua5.3-0
Use 'sudo apt autoremove' to remove them.
Do you want to continue? [Y/n]

List packages that …. depends on

Use apt-cache to list all the dependencies of the nmap package.

you@yourserver:~$ apt-cache depends nmap
nmap
Depends: libc6
Depends: libgcc1

Depends: zlib1g
Suggests: <ndiff>

List all packages that depends on …

Use apt-cache to list all  revers depends on nmap

user@yourserver:~$ apt-cache rdepends nmap
nmap
Reverse Depends:

python-nmap
plainbox-provider-checkbox
openvas-scanner

List the package that installed a particular file

Use dpkg to Search for packages that might have installed a file at /etc/init.d/ssh

you@yourserver:~$ dpkg -S /etc/init.d/ssh
openssh-server: /etc/init.d/ss

List all the files that a package has installed

Use dpkg-query to List all the files which the nmap package has installed (or will install) to your system.

you@yourserver:~$ dpkg-query -L nmap  
/usr/bin/ncat /usr/bin/nmap 
/usr/bin/nping /usr/share 
/usr/share/doc 
/usr/share/doc/nmap 
/usr/share/doc/nmap/3rd-party-licenses.txt.gz 

List installed packages

Use apt to list the installed packages on your system.

 you@yourserver:~$ sudo apt list --installed

Listing… Done
accountsservice/xenial-updates,now 0.6.40-2ubuntu11.3 amd64 [installed]
acl/xenial,now 2.2.52-3 amd64 [installed]
acpid/xenial,now 1:2.0.26-1ubuntu2 amd64 [installed]
adduser/xenial,now 3.113+nmu3ubuntu4 all [installed]
....

