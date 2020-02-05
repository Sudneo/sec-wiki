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




