# Nmap

This document contains a summary of useful Nmap commands grouped by functionality.

## Host Discovery

Ping sweep

```bash
nmap -sn 10.0.0.0/24
```

## Scanning 

### Idle Scan

```
# Pn is to avoid pings from our scanning host
nmap -Pn -sI <zombie IP>:<zombie port> <target IP>
```




