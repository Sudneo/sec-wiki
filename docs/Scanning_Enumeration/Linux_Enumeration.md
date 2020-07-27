# Linux Enumeration

## Remote Enumeration

Services that can distinguish a Linux host (and therefore identify it as such) include NFS and
Rpcbind.

### NFS

NFS sits on port 2049 UDP/TCP. It is used to expose exports, that on the host side are usually
configured under `/etc/exports`.

If a server has this port exposed, it is possible to check what exports are shared, and if we have
permissions to read or write on any of them. 
Nmap implements three NSE scripts which can do this more us:

```
nfs-ls
nfs-showmount
nfs-statfs
```

Alternatives to NSE scripts are:

```
# List exports
showmount -e
```

We can mount exposed shares with:

```
mount -t nfs IP:/path/export /mnt/mount/point [-o nolock]
```

### Rpcbind

This services is used to map RPC services to their UDP/TCP ports, and runs usually on TCP/UDP port
111 or 32771.

Nmap has NSE scripts for this port as well (`rpc-grind`, `rpcinfo`), otherwise it is possible to use native `rpcinfo` command.

```
rpcinfo -p IP
```

#### Enumerating Users with rpcclient

`rpcclient` can be used to enumerate usernames from Rpcbind.

```bash
for u in $(cat userlist.txt);
    do rpcclient -U "" IP -N \
    --command="lookupnames $u";
done| grep "User: 1"
```

Tools such as `enum4linux` already to much of the enumeration describe here for us, including user
enumeration.


### Samba

Samba is a Linux-based implementation of CIFS/SMB protocols. This acts similarly as how SMB shares
act on Windows and also can entail similar risks.

Samba can be found on usual NetBIOS ports:

```
137 tcp/udp - Name Service
138 tcp/udp - Datagram Service
129 tcp/udp - Session service
445 tcp     - Cifs
```

NSE scripts such as `smb-enum-shares` can be used to gather information about the shares. 
One common test is to verify whether anonymous access is allowed.

Alternatives to NSE scripts:

```
smbclient -L IP
```

Also, tools such as `smbmap` can provide information.

```
smbmap -H IP
```

If there is some share that we can access, we can mount it similarly to NFS.

```
# Install cifs-utils
mount -t cifs \\\\IP\\mount /mount/point
```


### SMTP

SMTP can be used as well to enumerate users using `RCPT`, `VRFY` and `EXPN` verbs.

#### RCPT

```
telnet IP 25
HELO domain
MAIL FROM: source@domain
RCPT TO user1@domain
...
RCPT TO user2@domain
```

If we get a code `250`, the user exists, otherwise, if we get `550` the user doesn't exist.

#### EXPN

This verb is supposed to be used to query a mail server for a list of members within a mailing list,
but can be used also to query a single user.

```
EXPN user
# 250 - Success
EXPN user2
# 550 - Failure
```

#### VRFY

This verb is more common that EXPN and can be used the same way:

```
VRFY user
# 250 - Success
VRFY user2
# 500 - Failure
```

Tools such as [smpt-user-enum](http://pentestmonkey.net/tools/user-enumeration/smtp-user-enum) can
be used to accomplish the same purpose.

```
smtp-user-enum -M METHOD -U userlist.txt -t IP/IPlist.txt
```

## Local Enumeration


### Network Enumeration

In addition to common commands, such as:

```
ip a s
ip route
ss -twurp
ss -patunl
arp -en
cat /etc/resolv.conf
```

To check network information we can directly parse `/proc` files, as described in [this
article](https://staaldraad.github.io/2017/12/20/netstat-without-netstat/).

Using `awk`:

```
awk 'function hextodec(str,ret,n,i,k,c){
    ret = 0
    n = length(str)
    for (i = 1; i <= n; i++) {
        c = tolower(substr(str, i, 1))
        k = index("123456789abcdef", c)
        ret = ret * 16 + k
    }
    return ret
}
function getIP(str,ret){
    ret=hextodec(substr(str,index(str,":")-2,2));
    for (i=5; i>0; i-=2) {
        ret = ret"."hextodec(substr(str,i,2))
    }
    ret = ret":"hextodec(substr(str,index(str,":")+1,4))
    return ret
}
NR > 1 {{if(NR==2)print "Local - Remote";local=getIP($2);remote=getIP($3)}{print local" - "remote}}' /proc/net/tcp
```

### System Information

Commands include:

```
# List all UID 0 accounts
cat /etc/passwd |cut -f1,3,4 -d":" |grep "0:0" |cut -f1 -d":"|awk '{print $1}'
cat /etc/passwd
cat /etc/shadow
sudo -l
cat /etc/sudoers
cat /root/.bash_history
find /home/* -name *.*history* -print 2> /dev/null
cat /etc/issue
cat /etc/*-release
ls -als /root/
env
cat /etc/crontab && ls -als /etc/cron*
# Find writeable cron jobs
find /etc/cron* -type f -perm -o+w -exec ls -l {} \;
ps auxxx
ps -u root
ps -u USER
# Find SUID files
find / -perm -4000 -type f 2>/dev/null
find / -uid 0 -perm -4000 -type f 2>/dev/null
# Find GUID files
find / -perm -2000 -type f 2>/dev/null
# Find world-writeable files
find -perm -2 -type f 2>/dev/null
# Find process binaries/paths and permissions
ps aux | awk ‘{print $11}’ |xargs -r ls -la 2>/dev/null |awk ‘!x[$0]++’
```

### Tools to do Enumeration

Many tools exist to provide a large amount of automated checks for us:

* [Linenum](https://github.com/rebootuser/LinEnum)
* [LinuxPrivChecker](https://github.com/sleventyeleven/linuxprivchecker)
* [Linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
* [Lse](https://github.com/diego-treitos/linux-smart-enumeration)
* [mimipenguin](https://github.com/huntergregal/mimipenguin) - Can dump users' passwords from memory
