## General Information about Scanning and Enumeration

### Port Reference

[IANA collects](http://www.iana.org/assignments/port-numbers) the data about port numbers
allocation. Although a service can bind on a port whihc is not reserved for it, this list contains
the default allocations.

### Scanning Fundamentals

#### TCP SYN Scan

Send a TCP SYN packet to destination. Oper ports will respond with SYN/ACK, closed ports will respond
    with RST flag. No response will be considered from the scanner as if the port is firewalled.

The scanner never finishes the connection establishment by sending an ACK message back (hence,
**half-open** scan), but instead aborts the connection with a RST packet.

SYN --> SYN/ACK or RST --> RST

#### TCP Connect Scan

Perform the full three-ways handshake with the destination, using the underlying OS functionality.
This means that the scanner, in general, will have less flexibility in what to put inside the
packets, since it is not assembling them directly (as it was for the TCP SYN scan).

SYN --> SYN/ACK or RST --> ACK --> RST

#### UDP Scan

Since UDP is a staless protocol, we need to send a full datagram. If the destination answers us with
a ICMP *Destination Unreacheable* we will know that the port is closed. In other cases, we will
asume the port is either Open or Firewalled.

The sender will have to start a timeout after every packet sent to measure when to assume that the
destination is not answering. Because of this, UDP scan can take very long.

UDP packet --> ICMP Destination Unreacheable or Data 

#### Idle Scan

Idle scans involve a zombie target, which should be a machine with almost no other traffic
received/sent, and the IP Fragmentation ID header. Many OS increment the Fragmentation ID by 1 every
packet, therefore the scan goes similarly to the following:

* Get Fragmentation ID of the zombie, by sending a packet to it. Save it as N.
* Spood a TCP SYN packet *from* the zombie to the target that needs to be scanned.
* Get again the Fragmentation ID from the zombie and measure the increment in N.

If the increment is of 1 only, then the port is closed, if it is of 2, then it is open.
In case of closed port, the zombie will receive a RST from the destination that will be ignored, and
later will send the answer to us (+1).
In case of open port, it will receive a SYN/ACK, to which it will respond with a RST (+1), and later
will answer us again (+1).

SYN (to zombie) --> SYN (to target) --> SYN (to zombie)

#### FTP Bound Scan

`PORT` command in FTP is used to let a FTP server perform a scan on our behalf.

FTP CONNECTION --> `PORT` COMMAND 

### Additional TCP Scans

TCP compliant system should answer with RST in case
a packet to a closed port is received without the RST flag, also, packets without SYN, RST or ACK
sent to an open port should be dropped.

Many OS nowadays return a RST in any cases, therefore breaking the following techniques.

#### NULL Scan

Performs a scan by not setting any TCP flag. Closed port will answer with RST. Open port will not
answer at all.

#### FIN Scan

Performs a scan by setting the `FIN` TCP flag. Closed port will answer with RST. Open port will not
answer at all.

#### Xmas Scan

Performs a scan by setting `FIN`, `PSH` and `URG` TCP flag. Closed port will answer with RST. Open port will not
answer at all.

### Ack Scan

This scan is **not** intended to determine open ports, but just to determine if some ports are
firewalled. The scanner sends a packet with `ACK` TCP flag set only. Open and closed ports should
answer with a RST packet. Filtered ports should not answer at all.











