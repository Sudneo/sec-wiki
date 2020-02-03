# Nmap

This document contains a summary of useful Nmap commands grouped by functionality.

## General Commands

Never perform DNS resolution

```
nmap -n [...]
```

## Host Discovery

Ping sweep

```bash
nmap -sn 10.0.0.0/24
```

TCP/UDP/SCTP discovery

```
# TCP SYN Discovery
nmap -PS <IP range>
# TCP ACK Discovery
nmap -PA<IP range>
# UDP Discovery
nmap -PU<IP range>
# SCTP Discovery
nmap -PY <IP range>
```

## Scanning 

### SYN Scan

```
nmap -sS <target IP>
```

### Connect Scan

```
nmap -sT <target IP>
```

### UDP Scan

```
# Slow
nmap -sU <target IP>
```

### Idle Scan

```
# Try to determine if this is a good host
nmap -O <zombie IP> 
# Pn is to avoid pings from our scanning host
nmap -Pn -sI <zombie IP>:<zombie port> <target IP>
```

### NULL Scan

```
nmap -sN <target IP>
```

### FIN Scan

```
nmap -sF <target IP>
```

### Xmas Scan

```
nmap -sX <target IP>
```

### FTP Bound Scan

```
nmap -b <FTP Relay> <target IP>
```

## Service Detection

The general way to do service detection.

```
nmap -sV <target IP>
```

Host fingerprinting

```
# Non  Aggressive
nmap -O <target IP>
# Aggressive
nmap -A <target IP>
```

## Firewall/IDS detection

More info can be found on [the official
documentation](https://nmap.org/book/man-bypass-firewalls-ids.html).

### Fragmentation

Fragmenting packets **might** prevent some IDS to detect the scan. Cannot be used with sT or sV
options.

```
nmap -f <target IP> --mtu [8xsize]
```

### Decoys

Decoys are just meant to increase the noise in the traffic.

```
nmap -D <Decoy IP 1>, <Decoy IP 2>, ME, <Decoy IP 3> <target IP>
```

### Timing

By tuning the timing of packets, we can limit the amount of packets sent in a specific timeframe,
avoiding time based rate-limiting.

```
# 0 is paranoid (5min), 5 is insane (5msec)
nmap -T[0-5] <target IP> [--max-retries N]
```

### Source Port

Some misconfigured firewalls might accept packets to some specific ports if coming from a specific
port (e.g, 53).

```
nmap --source-port/-g <source port> <target IP>
```



