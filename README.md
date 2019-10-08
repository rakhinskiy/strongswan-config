Do not install strongswan-libipsec (After install multiple routes not work)

yum
```
[root@gw ~]# rpm -qa | grep -E 'strongswan|ipsec'
strongswan-5.7.2-1.el7.x86_64
strongswan-tnc-imcvs-5.7.2-1.el7.x86_64
strongswan-sqlite-5.7.2-1.el7.x86_64
```

iptables

```
*mangle
:PREROUTING ACCEPT [4747:4257368]
:INPUT ACCEPT [4739:4255661]
:FORWARD ACCEPT [8:1707]
:OUTPUT ACCEPT [3641:3969240]
:POSTROUTING ACCEPT [3649:3970947]

-A FORWARD -p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
COMMIT

*nat
:PREROUTING ACCEPT [5:390]
:POSTROUTING ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]

-A POSTROUTING ! -d 192.168.0.0/16 -s 192.168.120.0/24 -j SNAT --to __EXTERNAL__IP__

COMMIT

*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]

-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT

-A INPUT -d __EXTERNAL__IP__ -p gre -j ACCEPT
-A INPUT -d __EXTERNAL__IP__ -p esp -j ACCEPT
-A INPUT -d __EXTERNAL__IP__ -p udp -m state --state NEW -m udp --dport 500  -j ACCEPT
-A INPUT -d __EXTERNAL__IP__ -p udp -m state --state NEW -m udp --dport 4500 -j ACCEPT

-A FORWARD -s 192.168.0.0/16 -j ACCEPT
-A FORWARD -d 192.168.0.0/16 -j ACCEPT
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
```

