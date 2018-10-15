TCP (Transmission Control Protocol)
UDP (User Datagram Protocol)

#FLAGS:
SYN - Initates a connection between two hosts to facilitate communication
ACK - Acknowledge the receipt of a packet
URG - Indicates that the data contained in the packet is urgent and should
      process it immediately
PSH - Insturcts the sending system to send all buffered data immediately
FIN - Tells te remote system about tthe end of the communiction. In essence,
      this gracefully closes the connection
RST - Reset a connection

Three-way handshake:
	-Establish a TCP conncetion

	SYN ->
	    <- SYN+ACK
	ACK ->

OSI Model:
	Application layer (HTTP, SNMP, ...)
	Presentation layer (MIME, ...)
	Session layer (SOCKS, NetBIOS, ...)
	Transport layer (TCP, UDP, ...)
	Network layer (IP, ICMP, ...)
	Data link layer (MAC, ARP, ...)
	Physical layer (ethernet, wifi, ...)

TCP/IP Model:
	Application layer (HTTP, SNMP, ...)
	Transport layer (TCP, UDP, ...)
	Internet layer (IP, ICMP, ...)
	Link layer (ARP, MAC, ...)

ARP packet
ICMP packet
Ping Sweep
SSDP (Simple Service Discovery Protocol)
DHCP (Dynamic Host Configuration Protocol)
DNS (Domian Name System)
UPnP (Universal Plug and Play)

#Scanning Techniques

TCP Connect() / Full Open Scan:
	-Three-way handshake
	-Completed connection
	-Logged and detected
	-Dont need ROOT
	-Open port:
		SYN ->
		    <- SYN+ACK
		ACK ->
		RST ->
	-Closed port:
		SYN ->
		    <- RST
	-Nmap: -sT

Stealth Scan / Half-Open Scan:
	-Half Three-way Handshake
	-Open Port:
		SYN ->
		    <- SYN+ACK
		RST ->
	-Closed port:
		SYN ->
		    <- RST
	-Nmpa: -sS

Inverse TCP Flag Scanning
	-send TCP probe with TCP flags (i.e. FIN, URG, PSH, without flag)
	-Xmas and Null scan

Xmas Scan:
	-PSH+URG+FIN flag or ALL flag
	-create abnormal situation
	-Open port:
		FIN+URG+PSH ->
			    <- No response
	-Closed port:
		FIN+URG+PSH ->
			    <- RST
	-Nmap: -sX

FIN Scan:
	-FIN scan work with RFC-793 based TCP/IP (before Win XP)
	-Only FIN flag
	-Probably pass firewalls
	-Open port:
		FIN ->
		    <- No response
	-Closed port:
		FIN ->
		    <- RST
	-Nmap: -sF

NULL Scan:
	-No flag
	-Easy to detect
	-Open port:
		NULL ->
		     <- No response
	-Closed port:
		NULL ->
		     <- RST
	-Nmap: -sN

ACK Flag probe scanning:
	-Only ACK flag
	-The response is always an RST
	-Examine the RST header (i.e. TTL, WINDOW), the decide if port open or
	 not
	-Help identify filtering system: RST mean no firewall, No response 
	 mean there is a firewall
	-Nmap: -sA

IDLE / IPID Header scan:
	-Remaining low profile
	-Scanning done by a zombie
	-Based on Full Open scan
	-The unsolicited SYN+ACK packet is ignored or responded with RST
	-Every IP packet has Fragment Identification Number (IPID)
	-OS increment IPID for each packet
	-Nmap: -sI <zombie host[:probeport]>
	-Method:
		-Send a SYN+ACK to zombie to get IPID
		-Zombie respond with RST
			SYN+ACK (IPID probe) ->
					     <- RST (IPID: 1234)
		-Send SYN packet to the target spoofing the IP of the zombie
		#PORT OPEN:
		-Target respond to the zombie with SYN+ACK
		-Zombie respond RST (IPID incremented by 1)
			SYN (spoofed IP) -> target
			target -> SYN+ACK -> zombie
			zombie -> RST -> target		 
		#PORT CLOSED:
		-Attacker send SYN packet with spoofed IP to the target
		-Target reply with RST
		-Zombie send nothing, so zombie's IPID not increased
			SYN (spoofed IP) -> target
			target -> RST -> zombie
		-Send SYN+ACK to the zombie again to get IPID
		-Zombie reply with RST (IPID increased)
		-Compare IPID
		-Port is open if IPID increased by 2 (+1 send to target,
		 +1 send to me)
		-Port is closed if IPID increased by 1 (+1,the last RST to me)

UDP Scan:
	-Connectionless protocol
	-Open port:
		UDP Port Probe ->
			       <- No response
	-Closed port:
		UDP Port Probe ->
			       <- ICMP Port Unreachable
	

IDS / IPS evasion:
	-packet fragmentation:
		-nmap: '-f'
		-The IDS have to reassemble the packets to detect an attack
	-sending packet with delay

OS Fingerprinting:
	-Active OS fingerprinting:
		-nmap: '-O'
		-send TCP and UDP packets and observe the response from
		 the host
	-Passive OS fingerprinting:
		-detail assessment of the traffic (TTL, TCP Window Size)
		-common values:
					TTL	TCP Window Size
			Linux		64	5840
			Windows XP	64	65535
			Windows 2008	64	8192
			FreeBSD		64	5840	
		
Banner Grabbing: 
	-determine the service
	-typically uses Telnet

Proxy:
	-system between the attacker and the target
	-hiding source IP address
	-impersonating
	-hide identity

Proxy chaning:
	-using multiple proxy server
	-most used proxy chains: TOR

Spoofing IP address
	-modify packet header
	-detect:
		-Direct TTL probe (on same subnet)
		-IP Identification Number
