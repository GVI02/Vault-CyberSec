#networking 

**Back to: [[Wireshark]]**

##### <span class="purple-highlight-light">NAMP Scans</span>

| Filter                                                | Description                 |
| ----------------------------------------------------- | --------------------------- |
| ```tcp.flags.syn == 1```                              | Detect SYN flag             |
| ```tcp.flags == 2```                                  | Detect **only** SYN flag    |
| ```tcp.flags.ack == 1```                              | Detect ACK flag             |
| ```tcp.flags == 16```                                 | Detect **only** ACK flag    |
| ```(tcp.flags.syn == 1) and (tcp.flags.ack == 1)```   | Detect SYN and ACK flag     |
| ```tcp.flags == 18```                                 | Detect **only** SYN and ACK |
| ```tcp.flags.reset == 1```                            | Detect RST flag             |
| ```tcp.flags == 4```                                  | Detect **only** RST flag    |
| ```(tcp.flags.reset == 1) and (tcp.flags.ack == 1)``` | Detect RST and ACK flag     |
| ```tcp.flags == 20```                                 | Detect **only** RST and ACK |
| ```tcp.flags.fin == 1```                              | Detect FIN flag             |
| ```tcp.flags == 1```                                  | Detect **only** FIN flag    |

###### <span class="blue-highlight-light">TCP Connect Scans</span>

- Relies on the three-way handshake (needs to finish the handshake process).
- Usually conducted with `nmap -sT` command.
- Used by non-privileged users (only option for a non-root user).
- Usually has a windows size larger than 1024 bytes as the request expects some data due to the nature of the protocol.

```
tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size > 1024
```

###### <span class="blue-highlight-light">SYN Scans</span>

- Doesn't rely on the three-way handshake (no need to finish the handshake process).
- Usually conducted with `nmap -sS` command.
- Used by privileged users.
- Usually have a size less than or equal to 1024 bytes as the request is not finished and it doesn't expect to receive data.

```
tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size <= 1024
```

###### <span class="blue-highlight-light">UDP Scans</span>

- Doesn't require a handshake process
- No prompt for open ports
- ICMP error message for close ports
- Usually conducted with `nmap -sU` command.

```
icmp.type == 3 and icmp.code == 3
```

##### <span class="purple-highlight-light">ARP Poisoning / Spoofing / Man in the Middle Attack</span>

Address Resolution Protocol Poisoning (also known as ARP Spoofing or Man In The Middle (MITM) attack) is a type of attack that involves network jamming/manipulating by sending malicious ARP packets to the default gateway. The ultimate aim is to manipulate the **"IP to MAC address table"** and sniff the traffic of the target host.

ARP analysis in a nutshell:
- Works on the local network
- Enables the communication between MAC addresses
- Not a secure protocol
- Not a routable protocol
- It doesn't have an authentication function
- Common patterns are request & response, announcement and gratuitous packets.

Possible ARP poisoning detection.
```
arp.duplicate-address-detected or arp.duplicate-address-frame
```

Possible ARP flooding from detection.
```
((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == target-mac-address)`
```

##### <span class="purple-highlight-light">Identifying Hosts: DHCP, NetBIOS and Kerberos</span>

