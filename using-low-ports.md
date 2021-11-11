
Securely binding low ports on linux

The well-known ports ranging from 0 to 1024 is often referred to as privileged ports. Most Linux operating systems will prevent non-root users from binding privileged ports. Running a service as root should never be an option as bugs or weaknesses in the service can give an attacker full control of your system.

So, what are your options when you want to bind a low port to service? In the examples below I will be using a web server named mywebserver which will bind to port 80 on an Ubuntu server.
Option 1: Use port forwarding with iptables

One option is to run mywebserver on a high port, lets say port 8080, and use port forwarding rules to forwarded the standard port 80 to port 8080. This can typically be done by using iptables (for local host request and requests from external hosts):

sudo iptables -t nat -I OUTPUT -p tcp -d 127.0.0.1 --dport 80 -j REDIRECT --to-ports 8080
sudo iptables -t nat -I PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8080

When using this approach you must make sure that you persist your changes, so that you don’t loose your newly created iptable configurations the next time you boot your server. This can typically be done by issuing an iptables-save command.

iptables-save
iptables-save > /etc/sysconfig/iptables
Caveats

    None if you are comfortable with iptables.

Option 2: Grant low port access by setcap

One option is to utilize a Linux kernel feature called Capabilities to grant the necessary capabilities on a per file basis.

    sudo setcap 'cap_net_bind_service=+ep' /path/to/mywebserver

Caveats

    Bugs or weaknesses in mywebserver can be used to run any service on any ports of your system.
    Any user having execution privileges on mywebserver can run it on privileged ports.
    If mywebserver rely on custom shared libraries (…/lib/…) it will fail since enabling capabilities will automatically disable loading of shared libraries via the LD_LIBRARY_PATH as a protection against hijacking by injecting bad libraries.
    If mywebserver is created with a script language, it will not work (the capabilities must be applied to the script run-time, which is a really bad idea)

Option 3: Grant port access by authbind

Assuming that your are running ubuntu, and have not yet installed authbind, you must start with:

apt install authbind

To enable a port for binding you simply need to create an empty file with the port number as a name in the /etc/autbind/byport folder and make it “executable” for fileowner. The is owner of this file will now have been given the permissions to bind to that port. In this example it will be the well-known http port (tcp port 80)

sudo touch /etc/authbind/byport/80
sudo chmod 500 /etc/authbind/byport/80

The permission to bind to the port can now be granted by simply giving ownership tho this file, in this example it will be the user named webuser.

sudo chown webuser /etc/authbind/byport/80

Now you are ready to run your service as webuser and bind to port 80.

su webuser
authbind -deep /path/to/mywebserver
Caveats

    Ports between 512 and 1023 inclusive can not be bound by authbind.
    Works only with ipv4 sockets
    May not operate correctly with multithreaded programs
    Other bugs and issues

References

    https://superuser.com/questions/710253/allow-non-root-process-to-bind-to-port-80-and-443
    https://unix.stackexchange.com/questions/10735/allowing-a-user-to-let-listen-to-a-port-below-1024
    https://stackoverflow.com/questions/9843178/linux-capabilities-setcap-seems-to-disable-ld-library-path
    https://stackoverflow.com/questions/413807/is-there-a-way-for-non-root-processes-to-bind-to-privileged-ports-on-linux
    https://mutelight.org/authbind
    https://manpages.ubuntu.com/manpages/precise/man1/authbind.1.html
    https://unix.stackexchange.com/questions/132663/how-do-i-drop-root-privileges-in-shell-scripts
