# Analyze-Network-Attacks
A scenario involving a customer of the company that you work for who experiences a security issue when accessing the company’s website. You will  identify the likely cause of the service interruption. Then, you will explain how the attack occurred and the negative impact it had on the website.

## Table of contents

1. [Scenario](#scenario)
2. [How to read Wireshark TCP/HTTP log](#how-to)
3. [The Attack](#attack)
4. [Placeholder](#assessment)
5. [Placeholder](#summary)

## Scenario <a name="scenario">
You work as a security analyst for a travel agency that advertises sales and promotions on the company’s website. The employees of the company regularly access the company’s sales webpage to search for vacation packages their customers might like. 

One afternoon, you receive an automated alert from your monitoring system indicating a problem with the web server. You attempt to visit the company’s website, but you receive a connection timeout error message in your browser.

You use a packet sniffer to capture data packets in transit to and from the web server. You notice a large number of TCP SYN requests coming from an unfamiliar IP address. The web server appears to be overwhelmed by the volume of incoming traffic and is losing its ability to respond to the abnormally large number of SYN requests. You suspect the server is under attack by a malicious actor. 

You take the server offline temporarily so that the machine can recover and return to a normal operating status. You also configure the company’s firewall to block the IP address that was sending the abnormal number of SYN requests. You know that your IP blocking solution won’t last long, as an attacker can spoof other IP addresses to get around this block. You need to alert your manager about this problem quickly and discuss the next steps to stop this attacker and prevent this problem from happening again. You will need to be prepared to tell your boss about the type of attack you discovered and how it was affecting the web server and employees.


## How to read Wireshark TCP/HTTP log <a name="how-to">

### Log entry number and time
| No. | Time |
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

## The Attack <a name="attack">
Malicious actors can take advantage of the TCP protocol by
flooding a server with SYN packet requests for the first part of the handshake. However, if the
number of SYN requests is greater than the server resources available to handle the
requests, then the server will become overwhelmed and unable to respond to the requests.
This is a network level denial of service (DoS) attack, called a SYN flood attack, that targets
network bandwidth to slow traffic. A SYN flood attack simulates a TCP connection and floods
the server with SYN packets. A DoS direct attack originates from a single source. A
distributed denial of service (DDoS) attack comes from multiple sources, often in different
locations, making it more difficult to identify the attacker or attackers.

| Color as text | No. | Time | Source | Destination | Protocol | Info |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| $\color{red}{\textsf{red}}$ | $\color{red}{\textsf{52}}$ | $\color{red}{\textsf{3.390692}}$ | $\color{red}{\textsf{203.0.113.0}}$ | $\color{red}{\textsf{192.0.2.1}}$ | $\color{red}{\textsf{TCP}}$ | $\color{red}{\textsf{54770->443 [SYN] Seq=0Win=5792 Len=0...}}$ |
| $\color{red}{\textsf{red}}$ | $\color{red}{\textsf{53}}$ | $\color{red}{\textsf{3.441926}}$ | $\color{red}{\textsf{192.0.2.1}}$ | $\color{red}{\textsf{203.0.113.0}}$ | $\color{red}{\textsf{TCP}}$ | $\color{red}{\textsf{443->54770 [SYN, ACK] Seq=0Win-5792Len=120...}}$ |
| $\color{red}{\textsf{red}}$ | $\color{red}{\textsf{54}}$ | $\color{red}{\textsf{3.493160}}$ | $\color{red}{\textsf{203.0.113.0}}$ | $\color{red}{\textsf{192.0.2.1}}$ | $\color{red}{\textsf{TCP}}$ | $\color{red}{\textsf{54770->443 [ACK Seq=1Win=5792 Len=0...}}$ |
| $\color{green}{\textsf{green}}$ | $\color{green}{\textsf{55}}$ | $\color{green}{\textsf{3.544394}}$ | $\color{green}{\textsf{198.51.100.14}}$ | $\color{green}{\textsf{192.0.2.1}}$ | $\color{green}{\textsf{TCP}}$ | $\color{green}{\textsf{14785->443 [SYN] Seq=0Win-5792 Len=120...}}$ |
| $\color{green}{\textsf{green}}$ | $\color{green}{\textsf{56}}$ | $\color{green}{\textsf{3.599628}}$ | $\color{green}{\textsf{192.0.2.1}}$ | $\color{green}{\textsf{198.51.100.14}}$ | $\color{green}{\textsf{TCP}}$ | $\color{green}{\textsf{443->14785 [SYN, ACK] Seq=0Win-5792 Len=120...}}$ |
| $\color{red}{\textsf{red}}$ | $\color{red}{\textsf{57}}$ | $\color{red}{\textsf{3.664863}}$ | $\color{red}{\textsf{203.0.113.0}}$ | $\color{red}{\textsf{192.0.2.1}}$ | $\color{red}{\textsf{TCP}}$ | $\color{red}{\textsf{54770->443 [SYN] Seq=0Win=5792 Len=0...}}$ |
| $\color{green}{\textsf{green}}$ | $\color{green}{\textsf{58}}$ | $\color{green}{\textsf{3.730097}}$ |  $\color{green}{\textsf{198.51.100.14}}$ | $\color{green}{\textsf{192.0.2.1}}$ | $\color{green}{\textsf{TCP}}$ | $\color{green}{\textsf{14785->443 [ACK] Seq=1Win-5792 Len=120...}}$ |
| $\color{red}{\textsf{red}}$ | $\color{red}{\textsf{59}}$ | $\color{red}{\textsf{3.795332}}$ | $\color{red}{\textsf{203.0.113.0}}$ | $\color{red}{\textsf{192.0.2.1}}$ | $\color{red}{\textsf{TCP}}$ | $\color{red}{\textsf{54770->443 [SYN] Seq=0Win-5792 Len=120...}}$ |
| $\color{green}{\textsf{green}}$ | $\color{green}{\textsf{60}}$ | $\color{green}{\textsf{3.860567}}$ | $\color{green}{\textsf{198.51.100.14}}$ | $\color{green}{\textsf{192.0.2.1}}$ | $\color{green}{\textsf{HTTP}}$ | $\color{green}{\textsf{GET /sales.html HTTP/1.1}}$ |
| $\color{red}{\textsf{red}}$ | $\color{red}{\textsf{60}}$ | $\color{red}{\textsf{3.939499}}$ | $\color{red}{\textsf{203.0.113.0}}$ | $\color{red}{\textsf{192.0.2.1}}$ | $\color{red}{\textsf{TCP}}$ | $\color{red}{\textsf{54770->443 [SYN] Seq=0Win-5792 Len=120...}}$ |
| $\color{green}{\textsf{green}}$ | $\color{green}{\textsf{62}}$ | $\color{green}{\textsf{4.018431}}$ | $\color{green}{\textsf{192.0.2.1}}$ | $\color{green}{\textsf{198.51.100.14}}$ | $\color{green}{\textsf{HTTP}}$ | $\color{green}{\textsf{HTTP/1.1 200 OK (text/html)}}$ |

Initially, the attacker’s SYN request is answered normally by the web server (log items 52-54). However, the attacker keeps sending more SYN requests, which is abnormal. At this point, the web server is still able to respond to normal visitor traffic, which is highlighted and labeled as green. An employee visitor with the IP address of 198.51.100.14 successfully completes a SYN/ACK connection handshake with the webserver (log item nos. 55, 56, 58). Then, the employee’s browser requests the sales.html webpage with the GET command and the web server responds (log item no. 60 and 62).

| Color as text | No. | Time | Source | Destination | Protocol | Info |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| $\color{green}{\textsf{green}}$ | $\color{green}{\textsf{63}}$ | $\color{green}{\textsf{4.097363}}$ | $\color{green}{\textsf{198.51.100.5}}$ | $\color{green}{\textsf{192.0.2.1}}$ | $\color{green}{\textsf{TCP}}$ | $\color{green}{\textsf{33638->443 [SYN] Seq=0 Win-5792 Len=120...}}$ |
| $\color{red}{\textsf{red}}$ | 64 | 4.176295 | 192.0.2.1 | 203.0.113.0 | TCP | 443->54770 [SYN, ACK] Seq=0 Win-5792 Len=120... |
| $\color{green}{\textsf{green}}$ | 65 | 4.255227 | 192.0.2.1 | 198.51.100.5 | TCP | 443->54770 [SYN, ACK] Seq=0 Win-5792 Len=120... |
| $\color{green}{\textsf{green}}$ | 65 | 4.255227 | 192.0.2.1 | 198.51.100.5 | TCP | 443->33638 [SYN, ACK] Seq=0 Win-5792 Len=120... |
| red | 66 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| $\color{green}{\textsf{green}}$ | 67 | 5.235091 | 198.51.100.5 | 192.0.2.1 | TCP | 33638->443 [ACK] Seq=1 Win-5792 Len=120... |
| red | 68 | 5.236023 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| $\color{green}{\textsf{green}}$ | 69 | 5.236955 | 198.51.100.16 | 192.0.2.1 | TCP | 32641->443 [SYN] Seq=0 Win-5792 Len=120... |
| red | 70 | 5.237887 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| $\color{green}{\textsf{green}}$ | 71 | 6.228728 | 198.51.100.5 | 192.0.2.1 | HTTP | GET /sales.html HTTP/1.1 |
| red | 72 | 6.229638 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| yellow | 73 | 6.230548 | 192.0.2.1 | 198.51.100.16 | TCP | 443->32641 [RST, ACK] Seq=0 Win-5792 Len=120... |
| red | 74 | 6.330539 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| $\color{green}{\textsf{green}}$ | 75 | 6.330885 | 198.51.100.7 | 192.0.2.1 | TCP | 42584->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 76 | 6.331231 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| yellow | 77 | 7.330577 | 192.0.2.1 | 198.51.100.5 | TCP | HTTP/1.1 504 Gateway Time-out (text/html) |
| red | 78 | 7.331323 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| $\color{green}{\textsf{green}}$ | 79 | 7.340768 | 198.51.100.22 | 192.0.2.1 | TCP | 6345->443 [SYN] Seq=0 Win=5792 Len=0... |
| yellow | 80 | 7.340773 | 192.0.2.1 | 198.51.100.7 | TCP | 443->42584 [RST, ACK] Seq=1 Win-5792 Len=120... |
| red | 81 | 7.340778 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 82 | 7.340783 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 83 | 7.439658 | 192.0.2.1 | 203.0.113.0 | TCP | 443->54770 [RST, ACK] Seq=1 Win=5792 Len=0... |

In the next 20 rows, the log begins to reflect the struggle the web server is having to keep up with the abnormal number of SYN requests coming in at a rapid pace. The attacker is sending several SYN requests every second. The rows highlighted and labeled yellow are failed communications between legitimate employee website visitors and the web server.

The two types of errors in the logs include:

-	An HTTP/1.1 504 Gateway Time-out (text/html) error message. This message is generated by a gateway server that was waiting for a response from the web server. If the web server takes too long to respond, the gateway server will send a timeout error message to the requesting browser.
-	An [RST, ACK] packet, which would be sent to the requesting visitor if the [SYN, ACK] packet is not received by the web server. RST stands for reset, acknowledge. The visitor will receive a timeout error message in their browser and the connection attempt is dropped. The visitor can refresh their browser to attempt to send a new SYN request.

| Color as text | No. | Time | Source | Destination | Protocol | Info |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| red | 119 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 120 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| Yellow | 121 | 19.844731 | 192.0.2.1 | 198.51.100.5 | TCP | 443->4631 [RST, ACK] Seq=0 Win-5792 Len=120... |
| red | 122 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 123 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 124 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 125 | 5.236023 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 126 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 127 | 5.237887 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 128 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 129 | 6.229638 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 130 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 131 | 6.330539 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 132 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 133 | 6.331231 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 134 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 135 | 7.331323 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 136 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 137 | 4.256159 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 138 | 7.340778 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 139 | 7.340783 | 203.0.113.0 | 192.0.2.1 | TCP | 54770->443 [SYN] Seq=0 Win=5792 Len=0... |
| red | 140 | 7.439658 | 192.0.2.1 | 203.0.113.0 | TCP | 443->54770 [RST, ACK] Seq=1 Win=5792 Len=0... |



