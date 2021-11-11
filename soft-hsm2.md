
SoftHSM2 on Ubuntu 18.04

The idea is to let end users use a software based HSM module without giving them read access to the private keys stored in the HSM, i.e. protecting the key much like a real HSM would do.

Disclaimer: The article is not complete and will only demonstrate the concept. I have not reviewed or scanned the source code refereed to and used below for any vulnerabilities.

SoftHSMv2 does not seem to work if you install it via APT install, but here is how to build it:

(apt update)
(apt upgrade -y)
(apt install gcc make automake libtool autoconf pkg-config libssl-dev -y)
git clone https://github.com/opendnssec/SoftHSMv2
cd SoftHSMv2/
sh autogen.sh
./configure
make
make install

Test it with pkcs11-tool from the opensc package.

apt install opensc
pkcs11-tool --module /usr/local/lib/softhsm/libsofthsm2.so \
 --init-token --label "token1" --slot 1 --so-pin 12345678
pkcs11-tool --module /usr/local/lib/softhsm/libsofthsm2.so \
 --init-pin --label "token1" --login
pkcs11-tool --module /usr/local/lib/softhsm/libsofthsm2.so \ 
--label "token1" --login --keypairgen --key-type rsa:2048 --usage-sign

Install pkcs11-proxy:

(apt update)
(apt upgrade -y)
apt install cmake libseccomp-dev libssl-dev -y
git clone https://github.com/SUNET/pkcs11-proxy
cd pkcs11-proxy/
mkdir build
cd build
cmake ..
make
make install

Test the proxy:

PKCS11_DAEMON_SOCKET="tcp://127.0.0.1:2345" pkcs11-daemon /usr/local/lib/softhsm/libsofthsm2.so &

PKCS11_PROXY_SOCKET="tcp://127.0.0.1:2345" pkcs11-tool --module /usr/local/lib/libpkcs11-proxy.so -I

This should be enough to demonstrate the concept. The concept can be extended with enabling TLS encrypted connection (shall be supported out of the box in the SUNET/pkcs11-proxy project) for which the softhsm can be hosted on a dedicated machine/container separate from the container accessing the softhsm via the proxy module. Logging and protection against multiple wrong PIN codes should be investigated etc.
