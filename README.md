**Network Packet Analysis with Wireshark**



Capturing and analyzing DNS, TCP, DHCP and ICMP traffic, and using packet analysis to diagnose a connectivity problem.



Tools:Wireshark 4.6.9, Windows, nslookup, curl, ping, ipconfig



Wireshark 4.6.9, Windows, nslookup, curl, ping, ipconfig



1\. DNS query and response



Goal: See how a name is resolved to an IP address.



Steps: Ran nslookup ikman.lk while capturing and used the filter dns.qry.name contains "ikman".



Observation: My PC sent an A query and an AAAA query to the DNS server over UDP port 53 and received the IPv4 addresses 172.66.145.108 and 104.20.46.87 and two IPv6 addresses in about 0.1 seconds.



Conclusion: DNS answers each query with a matching response, and a name has separate IPv4 (A) and IPv6 (AAAA) records.



DNS Screenshot:01-dns-query-response.png



2\. TCP three-way handshake



Goal: See how a TCP connection opens.



Steps: Ran curl http://ikman.lk while capturing and used the filter tcp.port == 80.



Observation: My PC sent a SYN from a high random port (50234) to port 80 on the server, the server replied with SYN-ACK, and my PC sent the final ACK.



Conclusion: TCP agrees on a connection with SYN, SYN-ACK and ACK before any web data is sent.



TCP Screenshot:02-tcp-handshake.png



3\. DHCP (DORA)



Goal: See how a device receives an IP address.



Steps: Ran ipconfig /release and ipconfig /renew while capturing and used the filter dhcp.



Observation: The exchange was Discover, Offer, Request and ACK, and the server's reply gave my PC a subnet mask, a default gateway, a DNS server and a lease time.



Conclusion: DHCP gives a device its address, mask, gateway and DNS server in four steps, the same settings I configure with ip dhcp pool in Packet Tracer.



DHCP Screenshot:03-dhcp-dora.png



4\. Troubleshooting: failed DNS lookup



Symptom: A name does not resolve.



Steps: Ran nslookup thisdomaindoesnotexist12345.com while capturing and used the filter dns.qry.name contains "thisdomaindoesnotexist".



Observation: The DNS server answered every query within about 50 ms with Reply code 3, "No such name" (NXDOMAIN), and zero answers.



Root cause: The domain does not exist, and the network itself is working.



Conclusion: A quick reply carrying an error code means DNS is reachable and the name is wrong, whereas no reply at all would point to a connection or DNS server problem.



NXDOMAIN Screenshot:04-dns-failure-nxdomain.png



5\. ICMP ping



Goal: See what a ping sends on the network.



Steps: Ran ping 8.8.8.8 while capturing and used the filter icmp.



Observation: Each ping appeared as an Echo request followed by an Echo reply, with replies arriving in about 20 ms.



Conclusion: Ping uses ICMP, and a reply proves the destination is reachable while showing the round-trip time.



ICMP Screenshot:05-icmp-ping.png



**What I learned**

How to use display filters to reduce thousands of captured packets to the few that matter.

How DNS, TCP and DHCP exchanges work step by step, and how to read their fields in Wireshark.

How a DNS error reply separates a wrong name from a network failure.

