#networking 

**Back to: [[Wireshark]]**

#### <span class="purple-highlight-light">NAMP Scans</span>

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

##### <span class="blue-highlight-light">TCP Connect Scans</span>

- Relies on the three-way handshake (needs to finish the handshake process).
- Usually conducted with `nmap -sT` command.
- Used by non-privileged users (only option for a non-root user).
- Usually has a windows size larger than 1024 bytes as the request expects some data due to the nature of the protocol.

```
tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size > 1024
```

##### <span class="blue-highlight-light">SYN Scans</span>

- Doesn't rely on the three-way handshake (no need to finish the handshake process).
- Usually conducted with `nmap -sS` command.
- Used by privileged users.
- Usually have a size less than or equal to 1024 bytes as the request is not finished and it doesn't expect to receive data.

```
tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size <= 1024
```

##### <span class="blue-highlight-light">UDP Scans</span>

- Doesn't require a handshake process
- No prompt for open ports
- ICMP error message for close ports
- Usually conducted with `nmap -sU` command.

```
icmp.type == 3 and icmp.code == 3
```

#### <span class="purple-highlight-light">ARP Poisoning / Spoofing / Man in the Middle Attack</span>

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

#### <span class="purple-highlight-light">Identifying Hosts: DHCP, NetBIOS and Kerberos</span>

##### <span class="blue-highlight-light">DHCP</span>

- Request: `dhcp.option.dhcp == 3`
- ACK: `dhcp.option.dhcp == 5`
- NAK: `dhcp.option.dhcp == 6`

DHCP Requests:
- **Option 12:** Hostname.
```
dhcp.option.hostname == "Galaxy-A12"
```
- **Option 50:** Requested IP address.
```
string(dhcp.option.requested_ip_address) == "172.16.13.85"
```
- **Option 51:** Requested IP lease time.
- **Option 61:** Client's MAC address.

DHCP ACK:
- **Option 15:** Domain name.
- **Option 51:** Assigned IP lease time.

DHCP NAK:
- **Option 56:** Message (rejection details/reason).

##### <span class="blue-highlight-light">NetBIOS</span>

**NBNS** options for grabbing the low-hanging fruits:
- **Queries:** Query details.

Query details could contain name, Time to live (TTL) and IP address details.

Registration requests
```
nbns.flags.opcode == 5
```

##### <span class="blue-highlight-light">Kerberos</span>

* CNAME - username
* SNAME - service and domain name for generated ticket

User account search:
```
kerberos.CNameString contains "keyword"
kerberos.CNameString and !(kerberos.CNameString contains "$" )
```

Some packets could provide hostname information in this field. To avoid this confusion, filter the `$` value. The values that end with `$` are hostnames, and the ones without it are user names.

Filters for protocol version, realm and SNAME
```
kerberos.pvno == 5
kerberos.realm contains ".org"
kerberos.SNameString == "krbtg"
```

#### <span class="purple-highlight-light">Tunneling Traffic: ICMP and DNS</span>

##### <span class="blue-highlight-light">ICMP</span>

Internet Control Message Protocol (ICMP) is designed for diagnosing and reporting network communication issues. It is highly used in error reporting and testing. As it is a trusted network layer protocol, sometimes it is used for denial of service (DoS) attacks; also, adversaries use it in data exfiltration and C2 tunneling activities.

Usually, ICMP tunneling attacks are anomalies appearing/starting after a malware execution or vulnerability exploitation. As the ICMP packets can transfer an additional data payload, adversaries use this section to exfiltrate data and establish a C2 connection. It could be a TCP, HTTP or SSH data. As the ICMP protocols provide a great opportunity to carry extra data, it also has disadvantages. Most enterprise networks block custom packets or require administrator privileges to create custom ICMP packets.

A large volume of ICMP traffic or anomalous packet sizes are indicators of ICMP tunneling. Still, the adversaries could create custom packets that match the regular ICMP packet size (64 bytes), so it is still cumbersome to detect these tunneling activities.

```
data.len > 64 and icmp
```

##### <span class="blue-highlight-light">DNS</span>

Similar to ICMP tunnels, DNS attacks are anomalies appearing/starting after a malware execution or vulnerability exploitation. Adversary creates (or already has) a domain address and configures it as a C2 channel. The malware or the commands executed after exploitation sends DNS queries to the C2 server. However, these queries are longer than default DNS queries and crafted for subdomain addresses. Unfortunately, these subdomain addresses are not actual addresses; they are encoded commands as shown below:

```
encoded-commands.maliciousdomain.com
```

When this query is routed to the C2 server, the server sends the actual malicious commands to the host. As the DNS queries are a natural part of the networking activity, these packets have the chance of not being detected by network perimeters.

```
dns contains "dnscat"
dns.qry.name.len > 15 and !mdns
```

#### <span class="purple-highlight-light">FTP</span>

##### <span class="blue-highlight-light">Brute force indicators</span>

List failed login attempts.
```
ftp.response.code == 530
```

List target username
```
(ftp.response.code == 530) and (ftp.response.arg contains "username")
```

##### <span class="blue-highlight-light">Password spray</span>

List targets for a static password
```
(ftp.request.command == "PASS" ) and (ftp.request.arg == "password")
```

#### <span class="purple-highlight-light">HTTP and HTTPS</span>

##### <span class="blue-highlight-light">HTTP</span>

Filter User agents. Additional information about user agents can be found at [[HTTP]]
```
(http.user_agent contains "sqlmap") or (http.user_agent contains "Nmap") or (http.user_agent contains "Wfuzz") or (http.user_agent contains "Nikto")
```

Filter type of server service
```
http.server contains "apache"
```

Filter server hostname
```
http.host contains "keyword"
```

Filter Keep-Alive connections
```
http.connection == "Keep-Alive"
```

##### <span class="blue-highlight-light">Log4j</span>

```
(ip contains "jndi") or ( ip contains "Exploit")
```
```
(frame contains "jndi") or ( frame contains "Exploit")
```
```
(http.user_agent contains "$") or (http.user_agent contains "==")
```

##### <span class="blue-highlight-light">HTTPS</span>

TLS Client Hello messages
```
(http.request or tls.handshake.type == 1) and !(ssdp)
```

TLS Server Hello messages
```
(http.request or tls.handshake.type == 2) and !(ssdp)
```

###### <span class="green-highlight-light">Decrypting HTTPS traffic</span>

An encryption key log file is a text file that contains unique key pairs to decrypt the encrypted traffic session. These key pairs are automatically created (per session) when a connection is established with an SSL/TLS-enabled webpage. As these processes are all accomplished in the browser, you need to configure your system and use a suitable browser (Chrome and Firefox support this) to save these values as a key log file. To do this, you will need to set up an environment variable and create the SSLKEYLOGFILE, and the browser will dump the keys to this file as you browse the web. SSL/TLS key pairs are created per session at the connection time, so it is important to dump the keys during the traffic capture. Otherwise, it is not possible to create/generate a suitable key log file to decrypt captured traffic. You can use the "right-click" menu or **"Edit --> Preferences --> Protocols --> TLS"** menu to add/remove key log files.

![[Pasted image 20250607001255.png]]

![[Pasted image 20250607001408.png]]
