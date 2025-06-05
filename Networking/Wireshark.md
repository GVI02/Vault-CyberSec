#networking 

**Back to: [[Networking]]**
**Next: [[Wireshark - Traffic Analysis]]**

#### <span class="purple-highlight-light">Filtering</span>

* **Capture Filters** - This type of filter is used to save only a specific part of the traffic. It is set before capturing traffic and not changeable during the capture.

* **Display Filters** - This type of filter is used to investigate packets by reducing the number of visible packets, and it is changeable during the capture.

The typical use case is capturing everything and filtering the packets according to the event of interest. Only experienced professionals use capture filters and sniff traffic. This is why Wireshark supports more protocol types in display filters.

##### <span class="blue-highlight-light">Capture Filter Syntax</span>

Capture filters have the following elements:
* Scope - host, net, port and portrange
* Direction - src, dst, src and dst, src or dst
* Protocol - ether, wlan, ip, ipv6, arp, rarp, tcp and udp

Example filter for capturing traffic on TCP port 80.
```
tcp port 80
```

Capture filters can be configured from Capture > Capture Filters.

##### <span class="blue-highlight-light">Display Filter Syntax</span>

This is Wireshark's most powerful feature. It supports 3000 protocols and allows conducting packet-level searches under the protocol breakdown.

>[!insight] Display filter reference
>https://www.wireshark.org/docs/dfref/

Example filter to display port 80 traffic:
```
tcp.port == 80
```

Wireshark has a built-in option (Display Filter Expression) that stores all supported protocol structures to help analysts create display filters. We will cover the "Display Filter Expression" menu later. Now let's understand the fundamentals of the display filter operations. A quick reference is available under the **"Analyse > Display Filters"** menu.

###### <span class="green-highlight-light">Comparison operators</span>

| English | C-like | Description              | Example                      |
| ------- | ------ | ------------------------ | ---------------------------- |
| eq      | ==     | Equal                    | ```ip.src == 10.10.10.100``` |
| ne      | !=     | Not equal                | ```ip.src != 10.10.10.100``` |
| gt      | >      | Greater than             | ```ip.ttl > 250```           |
| lt      | <      | Less than                | ```ip.ttl < 10```            |
| ge      | >=     | Greater than or equal to | ```ip.ttl >= 0xFA```         |
| le      | <=     | Less than or equal to    | ```ip.ttl <= 0xA```          |
Wireshark supports decimal and hexadecimal values in filtering. You can use any format you want according to the search you will conduct.

###### <span class="green-highlight-light">Logical operators</span>

| English | C-like | Description | Example                                                     |
| ------- | ------ | ----------- | ----------------------------------------------------------- |
| and     | &&     | Logical AND | ```(ip.src == 10.10.10.100) AND (ip.src == 10.10.10.111)``` |
| or      | \|\|   | Logical OR  | ```(ip.src == 10.10.10.100) OR (ip.src == 10.10.10.111)```  |
| not     | !      | Logical NOT | ```!(ip.src == 10.10.10.222)```                             |
>[!important]
>Usage of `!=value` is deprecated; using it could provide inconsistent results. Using the `!(value)` style is suggested for more consistent results.

###### <span class="green-highlight-light">Filter toolbar</span>

The filter toolbar is where you create and apply your display filters. It is a smart toolbar that helps you create valid display filters with ease. Before starting to filter packets, here are a few tips:  

- Packet filters are defined in lowercase.
- Packet filters have an autocomplete feature to break down protocol details, and each detail is represented by a "dot".
- Packet filters have a three-colour representation explained below.

<span class="green-bgrnd">Green</spann> - Valid Filter
<span class="red-bgrnd">Red</spann> - Invalid Filter
<span class="yellow-bgrnd">Yellow</spann> - Warning filter. This filter works, but it is unreliable, and it is suggested to change it with a valid filter.

![[Pasted image 20250602144548.png]]

![[Pasted image 20250602144602.png]]

As mentioned earlier, Wireshark has a built-in option (Display Filter Expression) that stores all supported protocol structures to help analysts create display filters. When an analyst can't recall the required filter for a specific protocol or is unsure about the assignable values for a filter, the Display Filter Expressions menu provides an easy-to-use display filter builder guide. It is available under the "Analyze --> Display Filter Expression" menu.

It is impossible to memorize all details of the display filters for each protocol. Each protocol can have different fields and can accept various types of values. The Display Filter Expressions menu shows all protocol fields, accepted value types (integer or string) and predefined values (if any). Note that it will take time and require practice to master creating filters and learning the protocol filter fields.

![[Pasted image 20250603123234.png]]



###### <span class="green-highlight-light">L2 Filters</span>

| Filter                                                                    | Description                            |
| ------------------------------------------------------------------------- | -------------------------------------- |
| ```arp```                                                                 | Show ARP packets                       |
| ```arp.opcode == 1```                                                     | ARP requests                           |
| ```arp.opcode == 2```                                                     | ARP responses                          |
| ```arp.dst.hw_mac == 00:00:00:00:00:00```                                 | Show packets from specific MAC address |
| ```arp.duplicate-address-detected```<br>```arp.duplicate-address-frame``` | Show duplicated-address ARP messages   |

###### <span class="green-highlight-light">IP Filters</span>

| Filter                         | Description                                                     |
| ------------------------------ | --------------------------------------------------------------- |
| ```ip```                       | Show app IP packets                                             |
| ```ip.addr == 10.10.10.11```   | Show all packets containing IP address 10.10.10.11              |
| ```ip.addr == 10.10.10.0/24``` | Show all packets containing IP addresses from 10.10.10.0/24 net |
| ```ip.src == 10.10.10.11```    | Show all packets originated from 10.10.10.111                   |
| ```ip.dst == 10.10.10.111```   | Show all packets sent to 10.10.10.111                           |
| ```ip.ttl < 10```              | Show all IP packets with a TTL less than 10                     |

###### <span class="green-highlight-light">TCP and UDP Filters</span>

| Filter                       | Description                                     |
| ---------------------------- | ----------------------------------------------- |
| ```tcp.port == 80```         | Show all TCP packets with port 80               |
| ```udp.port == 53```         | Show all UDP packets with port 53               |
| ```tcp.srcport == 1234```    | Show all TCP packets originating from port 1234 |
| ```udp.srcport == 1234```    | Show all UDP packets originating from port 1234 |
| ```tcp.dstport == 80```      | Show all TCP packets sent to port 80            |
| ```udp.dstport == 5353```    | Show all UDP packets sent to port 5353          |
| ```tcp.flags.syn == 1```     | Detect SYN flag                                 |
| ```tcp.flags == 2```         | Detect **only** SYN flag                        |
| ```tcp.flags.ack == 1```     | Detect ACK flag                                 |
| ```tcp.flags == 16```        | Detect **only** ACK flag                        |
| ```tcp.flags == 18```        | Detect **only** SYN and ACK                     |
| ```tcp.flags.reset == 1```   | Detect RST flag                                 |
| ```tcp.flags == 4```         | Detect **only** RST flag                        |
| ```tcp.flags == 20```        | Detect **only** RST and ACK                     |
| ```tcp.flags.fin == 1```     | Detect FIN flag                                 |
| ```tcp.flags == 1```         | Detect **only** FIN flag                        |
| ```tcp.window_size > 1024``` | Detect TCP window size greater than 1024        |

###### <span class="green-highlight-light">Application Level Protocol Filters</span>

| Filter                                           | Description                                                   |
| ------------------------------------------------ | ------------------------------------------------------------- |
| ```http```                                       | Show all HTTP packets                                         |
| ```http.response.code == 200```                  | Show all packets with HTTP response code "200"                |
| ```http.request.method == "GET"```               | Show all HTTP GET requests                                    |
| ```http.request.method == "POST"```              | Show all HTTP POST requests                                   |
| ```dns```                                        | Show all DNS packets                                          |
| ```dns.flags.response == 0```                    | Show all DNS requests                                         |
| ```dns.flags.response == 1```                    | Show all DNS responses                                        |
| ```dns.qry.type == 1```                          | Show all DNS "A" records                                      |
| ```icmp.type == 3```                             | Show all ICMP Destination Unreachable packets                 |
| ```icmp.code == 3```                             | Show all ICMP Port Unreachable packets                        |
| ```dhcp``` or ```bootp```                        | Show all DHCP packets                                         |
| ```dhcp.option.type == 50```                     | Show all DHCP packets that have Option 50                     |
| ```dhcp.option.dhcp == 3```                      | DHCP Request                                                  |
| ```dhcp.option.dhcp == 5```                      | DHCP ACK                                                      |
| ```dhcp.option.dhcp == 6```                      | DHCP NAK (denied requests)                                    |
| ```dhcp.option.hostname == "79-kw"```            | Show DHCP packets with Option 12 equal to 79-kw               |
| ```dhcp.option.requested_ip_address```           | Show DHCP packets with Option 50                              |
| ```dhcp.option.ip_address_lease_time```          | Show DHCP packets with Option 51                              |
| ```dhcp.option.type == 61```                     | Show DHCP packets with Option 61 (client MAC address)         |
| ```dhcp.option.domain_name```                    | Show packets with DHCP option 15                              |
| ```dhcp.option.domain_name contains "keyword"``` | Show packets with domain name option that contains "keyword"  |
| ```dhcp.option.type == 56```                     | Show DHCP packets with the message option set                 |
| ```nbns```                                       | Show NetBIOS packets                                          |
| ```nbns.flags.opcode == 5```                     | Show NetBIOS registration requests                            |
| ```nbns.name contains "keyword"```               | Show NetBIOS packets with "keyword" in the name field         |
| ```kerberos```                                   | Show Kerberos packets                                         |
| ```kerberos.CNameString contains "keyword"```    | Filter Kerberos packets where the username contains "keyword" |
| ```kerberos.pvno == 5```                         | Show Kerberos packets with protocol version 5                 |
| ```kerberos.realm contains ".org"```             | Show Kerberos packets with domain name ".org"                 |
| ```kerberos.SNameString == "krbtg"```            | Show Kerberos tickets with SNAME krbtg                        |

###### <span class="green-highlight-light">Advanced Filters</span>

* ```contains``` - Search a value inside packets. It is case-sensitive and provides similar functionality to the "Find" option by focusing on a specific field.

* ```matches``` - Search a pattern of a regular expression. It is case insensitive, and complex queries have a margin of error.

* ```in``` - Search a value or field inside of a specific scope/range.

* ```upper``` - Convert a string value to uppercase.

* ```lower``` - Convert a string value to lowercase.

* ```string``` - Convert a non-string value to a string.

| Filter                                        | Description                                                                                                     |
| --------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| ```http.server contains "Apache"```           | List all HTTP packets where packets' "server" field contains the "Apache" keyword.                              |
| ```http.host matches "\.(php\|html)"```       | List all HTTP packets where packets' "host" fields match keywords ".php" or ".html".                            |
| ```tcp.port in {80 443 8080}```               | List all TCP packets where packets' "port" fields have values 80, 443 or 8080.                                  |
| ```upper(http.server) contains "APACHE"```    | Convert all HTTP packets' "server" fields to uppercase and list packets that contain the "APACHE" keyword.      |
| ```lower(http.server) contains "apache"```    | Convert all HTTP packets' "server" fields info to lowercase and list packets that contain the "apache" keyword. |
| ```string(frame.number) matches "[13579]$"``` | Convert all "frame number" fields to string values, and list frames end with odd values.                        |

#### <span class="purple-highlight-light">Statistics</span>

This menu provides multiple statistics options ready to investigate to help users see the big picture in terms of the scope of the traffic, available protocols, endpoints and conversations, and some protocol-specific details like DHCP, DNS and HTTP/2.

##### <span class="blue-highlight-light">Resolved Addresses</span>

This option helps analysts identify IP addresses and DNS names available in the capture file by providing the list of the resolved addresses and their hostnames. Note that the hostname information is taken from DNS answers in the capture file.

![[Pasted image 20250602121937.png]]

##### <span class="blue-highlight-light">Protocol Hierarchy</span>

This option breaks down all available protocols from the capture file and helps analysts view the protocols in a tree view based on packet counters and percentages. Thus analysts can view the overall usage of the ports and services and focus on the event of interest. The golden rule is valid in this section; you can right-click and filter the event of interest.

![[Pasted image 20250602122910.png]]

##### <span class="blue-highlight-light">Conversations</span>

Conversation represents traffic between two specific endpoints. This option provides the list of the conversations in five base formats; ethernet, IPv4, IPv6, TCP and UDP. Thus analysts can identify all conversations and contact endpoints for the event of interest.

![[Pasted image 20250602123003.png]]

##### <span class="blue-highlight-light">Endpoints</span>

The endpoints option is similar to the conversations option. The only difference is that this option provides unique information for a single information field (Ethernet, IPv4, IPv6, TCP and UDP ).

Wireshark also supports resolving MAC addresses to human-readable format using the manufacturer name assigned by IEEE. Note that this conversion is done through the first three bytes of the MAC address and only works for the known manufacturers. When you review the ethernet endpoints, you can activate this option with the **"Name resolution"** button in the lower-left corner of the endpoints window.

![[Pasted image 20250602123203.png]]

Name resolution is not limited only to MAC addresses. Wireshark provides IP and port name resolution options as well. However, these options are not enabled by default. If you want to use these functionalities, you need to activate them through the **"Edit --> Preferences --> Name Resolution"** menu. Once you enable IP and port name resolution, you will see the resolved IP address and port names in the packet list pane and also will be able to view resolved names in the "Conversations" and "Endpoints" menus as well.

Besides name resolution, Wireshark also provides an IP geolocation mapping that helps analysts identify the map's source and destination addresses. But this feature is not activated by default and needs supplementary data like the GeoIP database. Currently, Wireshark supports MaxMind databases, and the latest versions of the Wireshark come configured MaxMind DB resolver. However, you still need MaxMind DB files and provide the database path to Wireshark by using the "Edit --> Preferences --> Name Resolution --> MaxMind database directories" menu. Once you download and indicate the path, Wireshark will automatically provide GeoIP information under the IP protocol details for the matched IP addresses.

![[Pasted image 20250602123449.png]]

![[Pasted image 20250602123453.png]]

##### <span class="blue-highlight-light">Protocol Details - IPv4 & IPv6</span>

The statistics menu has two options for narrowing the statistics on packets containing a specific IP version. Thus, analysts can identify and list all events linked to specific IP versions in a single window and use it for the event of interest. You can use the "Statistics --> IPvX Statistics" menu to view this info.

![[Pasted image 20250602125849.png]]

##### <span class="blue-highlight-light">DNS</span>

This option breaks down all DNS packets from the capture file and helps analysts view the findings in a tree view based on packet counters and percentages of the DNS protocol.

![[Pasted image 20250602130340.png]]

##### <span class="blue-highlight-light">HTTP</span>

This option breaks down all HTTP packets from the capture file and helps analysts view the findings in a tree view based on packet counters and percentages of the HTTP protocol. Thus analysts can view the HTTP service's overall usage, including request and response codes and the original requests. You can use the "Statistics --> HTTP" menu to view this info.

![[Pasted image 20250602130455.png]]


#### <span class="purple-highlight-light">Profiles</span>

Wireshark is a multifunctional tool that helps analysts to accomplish in-depth packet analysis. As we covered during the room, multiple preferences need to be configured to analyse a specific event of interest. It is cumbersome to re-change the configuration for each investigation case, which requires a different set of colouring rules and filtering buttons. This is where Wireshark profiles come into play. You can create multiple profiles for different investigation cases and use them accordingly. You can use the **"Edit --> Configuration Profiles"** menu or the **"lower right bottom of the status bar --> Profile"** section to create, modify and change the profile configuration.

![[Pasted image 20250603124733.png]]


