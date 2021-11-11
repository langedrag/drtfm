
Docker on Ubuntu 18.04

This guide will install the latest official docker community edition on an Ubuntu 18.04 server. Start with installing prerequisites for adding the official docker repository as a trusted repository on your machine:

    apt-transport-https (to support packages served via HTTPS)
    curl (to download package signing key)
    ca-certificates (to trust HTTPS server certificates like Mozilla would)
    software-properties-common (to add the docker repository)

sudo apt install apt-transport-https ca-certificates curl software-properties-common

Add and trust the docker repository signing key:

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

Add the docker repository:

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

Update apt to make the docker repository searchable:

sudo apt update

Install docker:

sudo apt install docker-ce

Make your user run docker without sudo:

sudo usermod -aG docker ${USER}

Make sure to log off and back on again to make sure your shell has the docker group privilege ass assigned above. Then test docker:

docker run hello-world
