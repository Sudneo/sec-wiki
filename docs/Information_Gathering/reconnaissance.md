# Reconnaissance

!!! warning
    Reconnaissance is an active method to collect information about a target, and requires the
    consent.
    
The objective of reconnaissance is to collect as much information as possible about:

* IP addresses
* DNS names
* Live Hosts


## Information about IPs

### Whois

Whois service runs on port `43` and can display information about IPs

```bash
whois $IP
```

Among the information that whois can give us, there is the DNS servers.

## DNS Information

Standard tools for DNS requests are:

* nslookup (mostly for Windows)
* dig

### nslookup

```bash
# Standard DNS query
nslookup domain.com

# Reverse DNS query
nslookup –type=PTR IPaddress

# MX (Mail Exchange) query
nslookup –type=MX domain.com

# Requesting DNS servers
nslookup –type=NS domain.com

# Zone Transfer attempt
nslookup
server [NAMESERVER FOR domain.com]
ls –d domain.com
```

### Dig

All the above commands can be accomplished with `dig` command as well.

```bash
# Standard DNS query
dig domain.com +short

# Reverse DNS query
dig domain.com PTR

# MX query
dig domain.com MX

# Nameserver query
dig domain.com NS

# Zone Transfer attempt
dig axfr @domain.com domain.com [+nocookie]
```

The +nocookie seems to be useful for a bug in Microsoft DNS server, as described [here](https://kevinlocke.name/bits/2017/01/20/formerr-from-microsoft-dns-server-for-dig/).


## Further Information

### DNS data mining

* [DNSDumpster](https://dnsdumpster.com)
* [DNSEnum](https://github.com/fwaeytens/dnsenum)

For example, to do DNS enumeration with dnsenum:

```
dnsenum --dnsserver DNS_IP DOMAIN
```

### Bing

Bing offers a filter that can limit the results to the site hosted on the specified IP.

`ip:X.X.X.X` will return the site(s) hosted on `X.X.X.X`.

### Other Sources

Other sources to gather information about specific IPs are:

* http://www.tcpiputils.com/domain-neighbors
* http://reverseip.domaintools.com/
* https://www.robtex.com/

## Host Discovery

To perform host discovery, refer to [Nmap short manual](../Scanning_Enumeration/nmap.md).





