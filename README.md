# Analyze-Network-Attacks
A scenario involving a customer of the company that you work for who experiences a security issue when accessing the company’s website. You will  identify the likely cause of the service interruption. Then, you will explain how the attack occurred and the negative impact it had on the website.

## Scenario
You work as a security analyst for a travel agency that advertises sales and promotions on the company’s website. The employees of the company regularly access the company’s sales webpage to search for vacation packages their customers might like. 

One afternoon, you receive an automated alert from your monitoring system indicating a problem with the web server. You attempt to visit the company’s website, but you receive a connection timeout error message in your browser.

You use a packet sniffer to capture data packets in transit to and from the web server. You notice a large number of TCP SYN requests coming from an unfamiliar IP address. The web server appears to be overwhelmed by the volume of incoming traffic and is losing its ability to respond to the abnormally large number of SYN requests. You suspect the server is under attack by a malicious actor. 

You take the server offline temporarily so that the machine can recover and return to a normal operating status. You also configure the company’s firewall to block the IP address that was sending the abnormal number of SYN requests. You know that your IP blocking solution won’t last long, as an attacker can spoof other IP addresses to get around this block. You need to alert your manager about this problem quickly and discuss the next steps to stop this attacker and prevent this problem from happening again. You will need to be prepared to tell your boss about the type of attack you discovered and how it was affecting the web server and employees.


## How to read Wireshark TCP/HTTP log

### Log entry number and time
| Mo. | Time |
| ----- | ----- |
| 47 | 3.144521 |
| 48 | 3.195755 |
| 49 | 3.246989 |

This Wireshark TCP log section provided to you starts at log entry number (No.) 47, which is
three seconds and .144521 milliseconds after the logging tool started recording. This indicates
that approximately 47 messages were sent and received by the web server in the 3.1 seconds
after starting the log. This rapid traffic speed is why the tool tracks time in milliseconds.

### Source and destination IP address
| Source | Destination |
| ----- | ----- |
| 198.51.100.23 | 192.0.2.1 |
| 192.0.2.1 | 198.51.100.23 |
| 198.51.100.23 | 192.0.2.1 |

The source and destination columns contain the source IP address of the machine that is
sending a packet and the intended destination IP address of the packet. In this log file, the IP
address 192.0.2.1 belongs to the company’s web server. The range of IP addresses in
198.51.100.0/24 belong to the employees’ computers.

### Protocol type and related information
| Protocol | Info |
| ----- | ----- |
| TCP | 42584->443 [SYN] Seq=0 Win-5792 Len=120... |
| TCP | 443->42584 [SYN, ACK] Seq=0 Win-5792 Len=120... |
| TCP | 42584->443 [ACK] Seq=1 Win-5792 Len=120... |

The Protocol column indicates that the packets are being sent using the TCP protocol, which
is at the transport layer of the TCP/IP model. In the given log file, you will notice that the
protocol will eventually change to HTTP, at the application layer, once the connection to the
web server is successfully established.
The Info column provides information about the packet. It lists the source port followed by an
arrow → pointing to the destination port. In this case, port 443 belongs to the web server.
Port 443 is normally used for encrypted web traffic.
The next data element given in the Info column is part of the three-way handshake process to
establish a connection between two machines. In this case, employees are trying to connect
to the company’s web server:
- The [SYN] packet is the initial request from an employee visitor trying to connect to a
web page hosted on the web server. SYN stands for “synchronize.”
- The [SYN, ACK] packet is the web server’s response to the visitor’s request agreeing
to the connection. The server will reserve system resources for the final step of the
handshake. SYN, ACK stands for “synchronize acknowledge.”
- The [ACK] packet is the visitor’s machine acknowledging the permission to connect.
This is the final step required to make a successful TCP connection. ACK stands for
“acknowledge.”

### Normal website traffic
| No. | Time | Source | Destination | Protocol | Info |
| ----- | ----- |----- | ----- | ----- | ----- |
| 47 | 3.144521 | 198.51.100.23 | 192.0.2.1 | TCP | 42584->443 [SYN] Seq=0 Win=5792Len=120... |
| 48 | 3.195755 | 192.0.2.1 | 198.51.100.23 | TCP | 443->42584 [SYN, ACK] Seq=0Win-5792 Len=120... |
| 49 | 3.246989 | 198.51.100.23 | 192.0.2.1 | TCP | 42584->443 [ACK] Seq=1 Win-5792Len=120... |
| 50 | 3.298223 | 198.51.100.23 | 192.0.2.1 | HTTP | GET /sales.html HTTP/1.1 |
| 51 | 3.349457 | 192.0.2.1 | 198.51.100.23 | HTTP | HTTP/1.1 200 OK (text/html) |

Notice that the handshake process takes a few milliseconds to complete. Then, you can
identify the employee’s browser requesting the sales.html webpage using the HTTP protocol
at the application level of the TCP/IP model. Followed by the web server responding to the
request.

# The Attack
malicious actors can take advantage of the TCP protocol by
flooding a server with SYN packet requests for the first part of the handshake. However, if the
number of SYN requests is greater than the server resources available to handle the
requests, then the server will become overwhelmed and unable to respond to the requests.
This is a network level denial of service (DoS) attack, called a SYN flood attack, that targets
network bandwidth to slow traffic. A SYN flood attack simulates a TCP connection and floods
the server with SYN packets. A DoS direct attack originates from a single source. A
distributed denial of service (DDoS) attack comes from multiple sources, often in different
locations, making it more difficult to identify the attacker or attackers.


