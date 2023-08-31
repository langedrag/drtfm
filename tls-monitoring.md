
# Network Inspection
## Inspect remote network with wireshark
This single line snippet shows how you can log on to server.example.com with SSH on port 22 in order to start remote tcpdump which is then forwarded to wireshark locally on your machine. This can be very usefull when you prefer to use a graphical user interface to inspect the traffic on a network interface on a remote device which only supports SSH and tcpdump.
```
ssh root@server.example.com -p 22 tcpdump -U -s0 'not port 22' -i eth0 -w - | wireshark -k -i -
```
## Inspect server names of TLS connection

Use tshark to do packet capture on-iinterface eth0 and apply a -filter to capture all TLS Client Hello packages (see below). Set the output format-Type to fields. Decode and print out the -elements frame.time, ip.src, ip.dst, tcp.dstport and tls.handshake.extension_server_name.
```
tshark -i eth0 -f "(tcp[((tcp[12:1] & 0xf0) >> 2):1] = 0x16)\
 and (tcp[((tcp[12:1] & 0xf0) >> 2)+5:1] = 0x01)" \
 -Tfields -e frame.time -e ip.src -e ip.dst \
 -e tcp.dstport -e tls.handshake.extensions_server_name
```
### The capture filter explained:
```
tcp[12:1]                           - 13th byte in the TCP header 
(tcp[12:1] & 0f0)                   - 4 high bits of the 13th byte
                                    - contains 32 bit data offset
tcp[((tcp[12:1] & 0xf0) >> 2)       - TCP data offset (byte size)
(tcp[((tcp[12:1] & 0xf0) >> 2):1]   - 1st byte in the TCP data =
=0x16)                              - Handshake (22) 
(tcp[((tcp[12:1] & 0xf0) >> 2)+5:1] - 6th byte in TCP data =
= 0x01)                             - Client Hello (1) 
```
