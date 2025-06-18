#networking 

**Back to: [[Wireshark]]**

TShark is a CLI version of Wireshark.

```shell
tshark
```

| Flag    | Description                                                                      |
| ------- | -------------------------------------------------------------------------------- |
| -h      | Help                                                                             |
| -v      | Show version                                                                     |
| -D      | List available interfaces                                                        |
| -i      | Choose an interface to capture live traffic. Interface number 1 used by default. |
| -r      | Read from file.                                                                  |
| -c      | Show first `n` packets                                                           |
| -w      | Write packets to a file                                                          |
| -V      | verbose output                                                                   |
| -q      | Silent mode. Suppresses packets in the terminal.                                 |
| -x      | Display packet bytes.                                                            |
| -a      | Capture conditions. Single loop.                                                 |
| -b      | Capture conditions. Infinite loop.                                               |
| -f      | Capture filters. Same as wireshark.                                              |
| -Y      | Display filters. Same as wireshark.                                              |
| --color | Colorized output                                                                 |
| -z      | Statistics                                                                       |

#### <span class="purple-highlight-light">Capture Conditions</span>

* Duration. Capture traffic for given time in seconds.
```shell
tshark -w test.pcap -a duration:1
```

* Filesize. Stop capture after capture given amount of data in KB.
```shell
tshark -w test.pcap -a filesize:10
```

* Files. Split output in multiple files.
```
thsark -w test.pcap -a filesize:10 -a file:3
```

#### <span class="purple-highlight-light">Statistics</span>

##### <span class="blue-highlight-light">Protocol Hierarchy</span>

* Show entire tree
```shell
tshark -r demo.pcapng -z io,phs
```

* Show UDP
```shell
tshark -r demo.pcapng -z io,phs,udp
```

##### <span class="blue-highlight-light">Packet Lengths Tree</span>

* Show packet lengths tree
```shell
tshark -r demo.pcapng -z plen,tree
```

##### <span class="blue-highlight-light">Endpoints</span>

* Show endpoints based on ip
```shell
tshark -r demo.pcapng -z endpoints,ip
```

* Show endpoints based on eth
```shell
tshark -r demo.pcapng -z endpoints,eth
```

Other filters: ipv6, tcp, udp, wlan

##### <span class="blue-highlight-light">Conversations</span>

* Show conversations
```shell
tshark -r demo.pcapng -z conv,ip
```

##### <span class="blue-highlight-light">Expert info</span>

* Show expert info. The expert info view helps analysts to view the automatic comments provided by Wireshark.
```shell
tshark -r demo.pcapng -z expert
```

##### <span class="blue-highlight-light">IPv4 and IPv6</span>

This option provides statistics on IPv4 and IPv6 packets

* Show protocol types
```shell
tshark -r demo.pcapng -z ptype,tree
```

* Show IPv4 hosts
```
tshark -r demo.pcapng -z ip_hosts,tree
```

* Show IPv6 hosts
```
tshark -r demo.pcapng -z ipv6_hosts,tree
```

* Show IPv4 sources and destinations
```
tshark -r demo.pcapng -z ip_srcdst,tree
```

* Show IPv6 sources and destinations
```
tshark -r demo.pcapng -z ipv6_srcdst,tree
```

* Show IPv4 destinations
```
tshark -r demo.pcapng -z dests,tree
```

* Show IPv6 destinations
```
tshark -r demo.pcapng -z ipv6_dests,tree
```

##### <span class="blue-highlight-light">DNS</span>

```shell
tshark -r demo.pcapng -z dns,tree
```

##### <span class="blue-highlight-light">HTTP</span>

* HTTP
```shell
tshark -r demo.pcapng -z http,tree
```

* HTTP2
```shell
tshark -r demo.pcapng -z http2,tree
```

* HTTP load distribution
```shell
tshark -r demo.pcapng -z http_srv,tree
```

* HTTP requests
```shell
tshark -r demo.pcapng -z http_req,tree
```

* HTTP requests and responses
```shell
tshark -r demo.pcapng -z http_seq,tree
```


#### <span class="purple-highlight-light">Streams</span>

* Follow stream
```shell
tshark -r demo.pcapng -z follow,[PROTOCOL],[VIEW MODE],[STREAM NUMBER]
```

Protocols: tcp, udp, http, http2
View mode: hex, ascii
Stream number: 0,1,2...


#### <span class="purple-highlight-light">Objects</span>

This option helps analysts to extract files from DICOM, HTTP, IMF, SMB and TFTP.

```shell
tshark -r demo.pcapng --export-objects [PROTOCOL],[TARGET FOLDER]
```

Protocol: dicom, http, imf, smb, tftp

Example, extract files from http traffic:
``
```shell
tshark -r demo.pcapng --export-objects http,/home/ubuntu/Desktop/captures
```


#### <span class="purple-highlight-light">Credentials</span>

```shell
tshark -r credentials.pcap -z credentials
```

#### <span class="purple-highlight-light">Fields</span>

You can extract specific fields from a capture.

| Flag | Description     |
| ---- | --------------- |
| -T   | main filter     |
| -e   | target field    |
| -E   | show field name |

```shell
tshark -r user-agents.pcap -T fields -e ip.src -e ip.dst -E header=y
```
```
OUTPUT:
ip.src	ip.dst
192.168.3.131	209.17.73.30
192.168.3.131	209.17.73.30
192.168.3.131	208.82.236.129
192.168.3.131	208.82.236.130
192.168.3.131	208.82.236.130
192.168.3.131	208.82.236.129
```

