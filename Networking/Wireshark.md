#networking 

#### <span class="purple-highlight-light">Filtering</span>

* **Capture Filters** - This type of filter is used to save only a specific part of the traffic. It is set before capturing traffic and not changeable during the capture.

* **Display Filters** - This type of filter is used to investigate packets by reducing the number of visible packets, and it is changeable during the capture.

The typical use case is capturing everything and filtering the packets according to the event of interest. Only experienced professionals use capture filters and sniff traffic. This is why Wireshark supports more protocol types in display filters.

##### <span class="purple-highlight-light">Capture Filter Syntax</span>

Capture filters have the following elements:
* Scope - host, net, port and portrange
* Direction - src, dst, src and dst, src or dst
* Protocol - ether, wlan, ip, ipv6, arp, rarp, tcp and udp

Example filter for capturing traffic on TCP port 80.
```
tcp port 80
```

Capture filters can be configured from Capture > Capture Filters.

##### <span class="purple-highlight-light">Display Filter Syntax</span>

This is Wireshark's most powerful feature. It supports 3000 protocols and allows conducting packet-level searches under the protocol breakdown.

>[!insight] Display filter reference
>https://www.wireshark.org/docs/dfref/

Example filter to display port 80 traffic:
```
tcp.port == 80
```

Wireshark has a built-in option (Display Filter Expression) that stores all supported protocol structures to help analysts create display filters. We will cover the "Display Filter Expression" menu later. Now let's understand the fundamentals of the display filter operations. A quick reference is available under the **"Analyse > Display Filters"** menu.

###### <span class="purple-highlight-light">Comparison operators</span>

| English | C-like | Description              | Example                      |
| ------- | ------ | ------------------------ | ---------------------------- |
| eq      | ==     | Equal                    | ```ip.src == 10.10.10.100``` |
| ne      | !=     | Not equal                | ```ip.src != 10.10.10.100``` |
| gt      | >      | Greater than             | ```ip.ttl > 250```           |
| lt      | <      | Less than                | ```ip.ttl < 10```            |
| ge      | >=     | Greater than or equal to | ```ip.ttl >= 0xFA```         |
| le      | <=     | Less than or equal to    | ```ip.ttl <= 0xA```          |
Wireshark supports decimal and hexadecimal values in filtering. You can use any format you want according to the search you will conduct.

###### <span class="purple-highlight-light">Logical operators</span>

| English | C-like | Description | Example                                                     |
| ------- | ------ | ----------- | ----------------------------------------------------------- |
| and     | &&     | Logical AND | ```(ip.src == 10.10.10.100) AND (ip.src == 10.10.10.111)``` |
| or      | \|\|   | Logical OR  | ```(ip.src == 10.10.10.100) OR (ip.src == 10.10.10.111)```  |
| not     | !      | Logical NOT | ```!(ip.src == 10.10.10.222)```                             |
>[!important]
>Usage of `!=value` is deprecated; using it could provide inconsistent results. Using the `!(value)` style is suggested for more consistent results.

###### <span class="purple-highlight-light">Filter toolbar</span>

The filter toolbar is where you create and apply your display filters. It is a smart toolbar that helps you create valid display filters with ease. Before starting to filter packets, here are a few tips:  

- Packet filters are defined in lowercase.
- Packet filters have an autocomplete feature to break down protocol details, and each detail is represented by a "dot".
- Packet filters have a three-colour representation explained below.

<span class="green-bgrnd">Green</spann> - Valid Filter
<span class="red-bgrnd">Red</spann> - Invalid Filter
<span class="yellow-bgrnd">Yellow</spann> - Warning filter. This filter works, but it is unreliable, and it is suggested to change it with a valid filter.

![[Pasted image 20250602144548.png]]

![[Pasted image 20250602144602.png]]

#### <span class="purple-highlight-light">Statistics</span>

This menu provides multiple statistics options ready to investigate to help users see the big picture in terms of the scope of the traffic, available protocols, endpoints and conversations, and some protocol-specific details like DHCP, DNS and HTTP/2.

##### <span class="purple-highlight-light">Resolved Addresses</span>

This option helps analysts identify IP addresses and DNS names available in the capture file by providing the list of the resolved addresses and their hostnames. Note that the hostname information is taken from DNS answers in the capture file.

![[Pasted image 20250602121937.png]]

##### <span class="purple-highlight-light">Protocol Hierarchy</span>

This option breaks down all available protocols from the capture file and helps analysts view the protocols in a tree view based on packet counters and percentages. Thus analysts can view the overall usage of the ports and services and focus on the event of interest. The golden rule is valid in this section; you can right-click and filter the event of interest.

![[Pasted image 20250602122910.png]]

##### <span class="purple-highlight-light">Conversations</span>

Conversation represents traffic between two specific endpoints. This option provides the list of the conversations in five base formats; ethernet, IPv4, IPv6, TCP and UDP. Thus analysts can identify all conversations and contact endpoints for the event of interest.

![[Pasted image 20250602123003.png]]

##### <span class="purple-highlight-light">Endpoints</span>

The endpoints option is similar to the conversations option. The only difference is that this option provides unique information for a single information field (Ethernet, IPv4, IPv6, TCP and UDP ).

Wireshark also supports resolving MAC addresses to human-readable format using the manufacturer name assigned by IEEE. Note that this conversion is done through the first three bytes of the MAC address and only works for the known manufacturers. When you review the ethernet endpoints, you can activate this option with the **"Name resolution"** button in the lower-left corner of the endpoints window.

![[Pasted image 20250602123203.png]]

Name resolution is not limited only to MAC addresses. Wireshark provides IP and port name resolution options as well. However, these options are not enabled by default. If you want to use these functionalities, you need to activate them through the **"Edit --> Preferences --> Name Resolution"** menu. Once you enable IP and port name resolution, you will see the resolved IP address and port names in the packet list pane and also will be able to view resolved names in the "Conversations" and "Endpoints" menus as well.

Besides name resolution, Wireshark also provides an IP geolocation mapping that helps analysts identify the map's source and destination addresses. But this feature is not activated by default and needs supplementary data like the GeoIP database. Currently, Wireshark supports MaxMind databases, and the latest versions of the Wireshark come configured MaxMind DB resolver. However, you still need MaxMind DB files and provide the database path to Wireshark by using the "Edit --> Preferences --> Name Resolution --> MaxMind database directories" menu. Once you download and indicate the path, Wireshark will automatically provide GeoIP information under the IP protocol details for the matched IP addresses.

![[Pasted image 20250602123449.png]]

![[Pasted image 20250602123453.png]]

##### <span class="purple-highlight-light">Protocol Details - IPv4 & IPv6</span>

The statistics menu has two options for narrowing the statistics on packets containing a specific IP version. Thus, analysts can identify and list all events linked to specific IP versions in a single window and use it for the event of interest. You can use the "Statistics --> IPvX Statistics" menu to view this info.

![[Pasted image 20250602125849.png]]

##### <span class="purple-highlight-light">DNS</span>

This option breaks down all DNS packets from the capture file and helps analysts view the findings in a tree view based on packet counters and percentages of the DNS protocol.

![[Pasted image 20250602130340.png]]

##### <span class="purple-highlight-light">HTTP</span>

This option breaks down all HTTP packets from the capture file and helps analysts view the findings in a tree view based on packet counters and percentages of the HTTP protocol. Thus analysts can view the HTTP service's overall usage, including request and response codes and the original requests. You can use the "Statistics --> HTTP" menu to view this info.

![[Pasted image 20250602130455.png]]

