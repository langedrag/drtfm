
# Network Inspection

## Inspect TLS connections on local host
Use tshark to do packet capture on-interface eth0 and apply a -filter to capture all TLS Client Hello packages (see below). Set the output format-Type to fields. Decode and print out the -elements frame.time, ip.src, ip.dst, tcp.dstport and tls.handshake.extension_server_name.
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
## Inspect TLS connections on remote router via SSH
SSH to a router and capture TLS client Hello packages with tcpdump and parse the packets with tshark locally. 
```
ssh root@router.example.com \
 'tcpdump -i any -s 0 -n -w - "tcp[((tcp[12:1] & 0xf0) >> 2):1] = 0x16 \
   and (tcp[((tcp[12:1] & 0xf0) >> 2) + 5:1] = 0x01)"' -w - | \
 tshark -r - -t ad -T fields \
   -e _ws.col.Time \
   -e ip.src \
   -e ip.dst \
   -e tcp.dstport \
   -e tls.handshake.extensions_server_name
```
