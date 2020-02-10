## Enumeration

The goal of enumeration is to gather more detailed information about devices and services (accounts,
shares, etc.).

### NetBIOS

Network Basic IO System is a service that allows **Windows** systems to share files, folders and
printers in LANs.

NetBIOS can run on TCP (NBT or NetBT), the protocol is specified in [RFC
1001](https://tools.ietf.org/html/rfc1001) and [RFC 1002](https://tools.ietf.org/html/rfc1002).

NetBIOS uses the following ports:

* UDP 137 for **name services**
* UDP 138 for **datagram services**
* TCP 139 for **session services**

#### Name Service

The name service is similar to DNS, translating NetBIOS names (unique 16-byte addresses) into IPs.
Of the 16 bytes that compose a name, the first 15 can be chosen by the user, while the 16th is
reserved for values from 00 to FF that identify the type of the device (e.g., name00 is for a
workstation).

Microsoft [documentation](https://msdn.microsoft.com/en-us/library/cc224454.aspx) has a complete
list of suffixes.

To see the names associated with own machine, we can run:

```
nbtstat -n
```

#### Datagram Service

NetBIOS Datagram Service (NBDS) allows the sending of messages to NetBIOS names, including to all of
them (Broadcast).
The communication is running on UDP and it is stateless, but doesn't provide also error correction
or retrasmission.

#### Session Service

NBSS (NetBIOS Session Service) is the service that allows the establishment of a connection between
to NetBIOS names, so that they can exchange data. To establish a connection:

* WINS is used to resolve the NetBIOS name into IP.
* A TCP connection on port 139 is established between the devices.
* The initiator sends a NetBIOS Session Request over the TCP connection established, including
  information such as the NetBIOS name of the application which wants to exchange data and the one
  of the destination.
* If the destination exists, or is listening on that name, the session will be established.

#### SMB

Server Message Block is a protocol that is very used in Windows enviornments, and allows for sharing
of files, directories, printers, messaging and more. It runs on TCP port 445.

### Tools of the trade

* nbtstat (Win)
* nbtscan (Linux)
* smbclient

To gather information about a target we can start with

```bash
nbtscan -v IP[/Mask]
```

List shares

```bash
# Note that shares that contain $ symbol are hidden
smbclient -L IP
```

Mount the share locally

```bash
mount.cifs //IP/SHARE_NAME /media/K_share/ user=,pass=
smbclient \\\\IP\\SHARE
```

Gather information about shares etc.

```bash
enum4linux -a IP
```

Execute RPC in the target

```
# Establishes a NULL session with IP
rpcclient -N -U "" IP
rpcclient $> CMD
```

There are many commands we can execute once the RPC connection is established.


### SNMP

Simple Network Management Protocol, used to exchange information between network devices. The
protocol has the concept of manager and agents, where the agents either wait for commands from the
manager or send traps to it.

There are 4 basic operations:

* Read. Used to monitor devices.
* Write. Used to configure devices.
* Trap. Used to trap events from the device and report to the monitoring system.
* Traversal Operations. Used to explore the options that a certain device supports.

#### Working Mechanism

SNMP uses 2 ports:

* UDP 161 for general messages
* UDP 162 for traps

As there are agents and manager, the protocol has 4 verbs that the manager can send to the agents:
`GET`, `GetNext`, `Set` and `Trap`.
To perform authentication, SNMP uses the so called **community string**. This can be either private,
allowing for modifications, or public, allowing only read operations.

There is a collection of definitions for the properties of specific devices, which is called MIB
(Management Information Base), and it is organized as a tree. Each node of the tree has some ID and
a name. The complete sequence of IDs from the top of the tree to the leaf, is called OID (Object
Identifier), and it is of the form `1.3.6.1.X.Y[.J]+`. 

#### Attacks Against SNMP

##### Obtaining Community String

SNMPv1 and SNMPv2 are cleartext protocols, so the most straightforward way to obtain the Community
String is to just sniff the traffic.
Bruteforcing the key is an option that is always available, even with SNMPv3 (which is encrypted),
although it is a noisy technique and it might be blocked by IDSs.

#### Tools of the Trade

##### snmpwalk

`snmpwalk` is used to gather information about devices in a tree. It used `GetNext` to gather
information about as many devices as it can. 

```bash
snmpwalk -v <SNMP_Version> <Target_IP> -c <Community_String>
# snmpwalk -v 2c 10.10.100.1 -c public
```

##### snmpset

`snmpset` is used to change/set configuration or information on an entity.

```bash
# s is for STRING
snmpset -v 2c -c public 10.10.100.1 system.sysContact.0 s admin@test.com
```

##### Nmap

Some SNMP related NSE scripts are:

* snmp-brute
* snmp-info
* snmp-interfaces
* snmp-netstat
* snmp-processes
* snmp-sysdescr
* snmp-win32-services

```bash
# Enumerate services on the machine
nmap -sU -p 161 --script=snmp-win32-services 10.10.100.1
# Bruteforce the community string
# Default wordlist /usr/share/nmap/nselib/data/snmpcommunities.lst
nmap -sU -p 161 --script=snmp-brute 10.10.100.1 [--script-args snmp-brute.communitiesdb=<wordlist>]
```

!!! note
    [Seclists](https://github.com/danielmiessler/SecLists) has a list with common SNMP Community
    Strings.
