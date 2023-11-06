# TP2 : Environnement virtuel
# I. Topologie réseau
☀️ Sur **`node1.lan1.tp2`**

- afficher ses cartes réseau
```
[node1.lan1.tp2@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:31:25:fa brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe31:25fa/64 scope link
       valid_lft forever preferred_lft forever
```
- afficher sa table de routage

```
[node1.lan1.tp2@localhost ~]$ ip r s
default via 10.1.1.254 dev enp0s8 proto static metric 100
10.1.1.0/24 dev enp0s8 proto kernel scope link src 10.1.1.11 metric 100
10.1.2.0/24 via 10.1.1.254 dev enp0s8 proto static metric 100
```
- prouvez qu'il peut joindre `node2.lan2.tp2`
```
[node1.lan1.tp2@localhost ~]$ ping 10.1.2.12
PING 10.1.2.12 (10.1.2.12) 56(84) bytes of data.
64 bytes from 10.1.2.12: icmp_seq=1 ttl=63 time=2.05 ms
64 bytes from 10.1.2.12: icmp_seq=2 ttl=63 time=2.04 ms
64 bytes from 10.1.2.12: icmp_seq=3 ttl=63 time=2.08 ms
64 bytes from 10.1.2.12: icmp_seq=4 ttl=63 time=1.79 ms
^C
--- 10.1.2.12 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 1.793/1.989/2.080/0.114 ms
```
- prouvez avec un `traceroute` que le paquet passe bien par `router.tp2`

```
[node1.lan1.tp2@localhost ~]$ traceroute 10.1.2.12
traceroute to 10.1.2.12 (10.1.2.12), 30 hops max, 60 byte packets
 1  _gateway (10.1.1.254)  0.600 ms  0.849 ms  1.004 ms
 2  10.1.2.12 (10.1.2.12)  1.652 ms !X  1.533 ms !X  1.529 ms !X
```

# II. Interlude accès internet
☀️ **Sur `router.tp2`**

- prouvez que vous avez un accès internet (ping d'une IP publique)

```
[router.tp2@localhost ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=57 time=12.2 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=57 time=13.1 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=57 time=12.0 ms
64 bytes from 1.1.1.1: icmp_seq=4 ttl=57 time=12.4 ms
^C
--- 1.1.1.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3225ms
rtt min/avg/max/mdev = 11.967/12.408/13.097/0.428 ms
```
- prouvez que vous pouvez résoudre des noms publics (ping d'un nom de domaine public)
```
[router.tp2@localhost ~]$ ping ynov.com
PING ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=1 ttl=57 time=11.4 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=2 ttl=57 time=11.5 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=3 ttl=57 time=12.0 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=4 ttl=57 time=12.7 ms
^C
--- ynov.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
rtt min/avg/max/mdev = 11.374/11.886/12.714/0.527 ms
```
☀️ **Accès internet LAN1 et LAN2**

- ajoutez une route par défaut sur les deux machines du LAN1
```
[node1.lan1.tp2@localhost ~]$ sudo ip route add default via 10.1.1.254 dev enp0s8
```
```
[node2.lan1.tp2@localhost ~]$ sudo ip route add default via 10.1.1.254 dev enp0s8
```
- ajoutez une route par défaut sur les deux machines du LAN2
```
[node1.lan2.tp2@localhost ~]$ sudo ip route add default via 10.1.2.254 dev enp0s8
```
```
[node2.lan2.tp2@localhost ~]$ sudo ip route add default via 10.1.2.254 dev enp0s8
```

- configurez l'adresse d'un serveur DNS que vos machines peuvent utiliser pour résoudre des noms
- dans le compte-rendu, mettez-moi que la conf des points précédents sur `node2.lan1.tp2`
```
[node2.lan1.tp2@localhost ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s8
NAME=enp0s8
DEVICE=enp0s8

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.1.1.12
NETMASK=255.255.255.0

GATEWAY=10.1.1.254
DNS1=1.1.1.1
```
- prouvez que `node2.lan1.tp2` a un accès internet :
  - il peut ping une IP publique
```
[node2.lan1.tp2@localhost ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=56 time=13.1 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=56 time=12.9 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=56 time=13.2 ms
64 bytes from 1.1.1.1: icmp_seq=4 ttl=56 time=12.5 ms
^C
--- 1.1.1.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3015ms
rtt min/avg/max/mdev = 12.529/12.936/13.216/0.256 ms
```
- il peut ping un nom de domaine public

```
[node2.lan1.tp2@localhost ~]$ ping google.com
PING google.com (142.251.37.238) 56(84) bytes of data.
64 bytes from mrs09s16-in-f14.1e100.net (142.251.37.238): icmp_seq=1 ttl=113 time=18.0 ms
64 bytes from mrs09s16-in-f14.1e100.net (142.251.37.238): icmp_seq=2 ttl=113 time=19.1 ms
64 bytes from mrs09s16-in-f14.1e100.net (142.251.37.238): icmp_seq=3 ttl=113 time=20.0 ms
64 bytes from mrs09s16-in-f14.1e100.net (142.251.37.238): icmp_seq=4 ttl=113 time=18.5 ms
^C
--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3012ms
rtt min/avg/max/mdev = 17.984/18.897/20.007/0.759 ms
```
# III. Services réseau
## 1. DHCP

☀️ **Sur `dhcp.lan1.tp2`**
- changez son adresse IP en `10.1.1.253`
```
[dhcp.lan1.tp2@localhost ~]$ ip a

[...]

2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:71:69:e3 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.253/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe71:69e3/64 scope link
       valid_lft forever preferred_lft forever
```
- setup du serveur DHCP
  - commande d'installation du paquet
```
  [dhcp.lan1.tp2@localhost ~]$ sudo dnf -y install dhcp-server
[sudo] password for dhcp.lan1.tp2:
Last metadata expiration check: 0:19:07 ago on Mon 06 Nov 2023 02:27:17 AM CET.
Dependencies resolved.
============================================================================================================================================================
 Package                               Architecture                     Version                                      Repository                        Size
============================================================================================================================================================
Installing:
 dhcp-server                           x86_64                           12:4.4.2-18.b1.el9                           baseos                           1.2 M
Installing dependencies:
 dhcp-common                           noarch                           12:4.4.2-18.b1.el9                           baseos                           128 k

Transaction Summary
============================================================================================================================================================
Install  2 Packages

Total download size: 1.3 M
Installed size: 4.2 M
Downloading Packages:
(1/2): dhcp-common-4.4.2-18.b1.el9.noarch.rpm                                                                               1.0 MB/s | 128 kB     00:00
(2/2): dhcp-server-4.4.2-18.b1.el9.x86_64.rpm                                                                               5.2 MB/s | 1.2 MB     00:00
------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                       2.8 MB/s | 1.3 MB     00:00
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                                    1/1
  Installing       : dhcp-common-12:4.4.2-18.b1.el9.noarch                                                                                              1/2
  Running scriptlet: dhcp-server-12:4.4.2-18.b1.el9.x86_64                                                                                              2/2
  Installing       : dhcp-server-12:4.4.2-18.b1.el9.x86_64                                                                                              2/2
  Running scriptlet: dhcp-server-12:4.4.2-18.b1.el9.x86_64                                                                                              2/2
  Verifying        : dhcp-server-12:4.4.2-18.b1.el9.x86_64                                                                                              1/2
  Verifying        : dhcp-common-12:4.4.2-18.b1.el9.noarch                                                                                              2/2

Installed:
  dhcp-common-12:4.4.2-18.b1.el9.noarch                                        dhcp-server-12:4.4.2-18.b1.el9.x86_64

Complete!
```
- fichier de conf

```
[dhcp.lan1.tp2@localhost ~]$ sudo cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#

# create new
# specify domain name
option domain-name     "lan1.tp2";
# specify DNS server's hostname or IP address
option domain-name-servers     dhcp.lan1.tp2;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnetmask
subnet 10.1.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.1.1.100 10.1.1.200;
    # specify broadcast address
    option broadcast-address 10.1.1.255;
    # specify gateway
    option routers 10.1.1.254;
}
```
- service actif
```
[dhcp.lan1.tp2@localhost ~]$ systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Mon 2023-11-06 02:53:33 CET; 4s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 11308 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 11127)
     Memory: 5.2M
        CPU: 14ms
     CGroup: /system.slice/dhcpd.service
             └─11308 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Config file: /etc/dhcp/dhcpd.conf
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Database file: /var/lib/dhcpd/dhcpd.leases
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: PID file: /var/run/dhcpd.pid
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Source compiled to use binary-leases
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Wrote 0 leases to leases file.
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Listening on LPF/enp0s8/08:00:27:71:69:e3/10.1.1.0/24
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Sending on   LPF/enp0s8/08:00:27:71:69:e3/10.1.1.0/24
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Sending on   Socket/fallback/fallback-net
Nov 06 02:53:33 localhost.localdomain dhcpd[11308]: Server starting service.
Nov 06 02:53:33 localhost.localdomain systemd[1]: Started DHCPv4 Server Daemon.
```
☀️ **Sur `node1.lan1.tp2`**

- demandez une IP au serveur DHCP
```
[node1.lan1.tp2@localhost ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s8
NAME=enp0s8
DEVICE=enp0s8

BOOTPROTO=dhcp
ONBOOT=yes
```

- prouvez que vous avez bien récupéré une IP *via* le DHCP
```
[node1.lan1.tp2@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:31:25:fa brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.100/24 brd 10.1.1.255 scope global dynamic noprefixroute enp0s8
       valid_lft 567sec preferred_lft 567sec
    inet6 fe80::a00:27ff:fe31:25fa/64 scope link
       valid_lft forever preferred_lft forever
```
- prouvez que vous avez bien récupéré l'IP de la passerelle
```
[node1.lan1.tp2@localhost ~]$ ip r s
default via 10.1.1.254 dev enp0s8 proto dhcp src 10.1.1.100 metric 100
10.1.1.0/24 dev enp0s8 proto kernel scope link src 10.1.1.100 metric 100
10.1.2.0/24 via 10.1.1.254 dev enp0s8 proto static metric 100
```
- prouvez que vous pouvez `ping node1.lan2.tp2`
```
[node1.lan1.tp2@localhost ~]$ ping 10.1.2.11
PING 10.1.2.11 (10.1.2.11) 56(84) bytes of data.
64 bytes from 10.1.2.11: icmp_seq=1 ttl=63 time=3.46 ms
64 bytes from 10.1.2.11: icmp_seq=2 ttl=63 time=1.32 ms
64 bytes from 10.1.2.11: icmp_seq=3 ttl=63 time=1.50 ms
64 bytes from 10.1.2.11: icmp_seq=4 ttl=63 time=1.84 ms
^C
--- 10.1.2.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3187ms
rtt min/avg/max/mdev = 1.320/2.030/3.463/0.847 ms
```
☀️ **Sur `web.lan2.tp2`**
- setup du service Web
  - installation de NGINX
  - gestion de la racine web `/var/www/site_nul/`
  - configuration NGINX
  - service actif
  - ouverture du port firewall
- prouvez qu'il y a un programme NGINX qui tourne derrière le port 80 de la machine (commande `ss`)
- prouvez que le firewall est bien configuré
```
[web.lan2.tp2@localhost ~]$ sudo dnf install nginx

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for web.lan2.tp2:
Rocky Linux 9 - BaseOS                                                                                                       11 kB/s | 4.1 kB     00:00
Rocky Linux 9 - BaseOS                                                                                                      4.3 MB/s | 1.9 MB     00:00
Rocky Linux 9 - AppStream                                                                                                    13 kB/s | 4.5 kB     00:00
Rocky Linux 9 - AppStream                                                                                                    11 MB/s | 7.1 MB     00:00
Rocky Linux 9 - Extras                                                                                                       12 kB/s | 2.9 kB     00:00
Rocky Linux 9 - Extras                                                                                                       36 kB/s |  11 kB     00:00
Dependencies resolved.
============================================================================================================================================================
 Package                                  Architecture                  Version                                      Repository                        Size
============================================================================================================================================================
Installing:
 nginx                                    x86_64                        1:1.20.1-14.el9_2.1                          appstream                         36 k
Installing dependencies:
 nginx-core                               x86_64                        1:1.20.1-14.el9_2.1                          appstream                        565 k
 nginx-filesystem                         noarch                        1:1.20.1-14.el9_2.1                          appstream                        8.5 k
 rocky-logos-httpd                        noarch                        90.14-1.el9                                  appstream                         24 k

Transaction Summary
============================================================================================================================================================
Install  4 Packages

Total download size: 634 k
Installed size: 1.8 M
Is this ok [y/N]: y
Downloading Packages:
(1/4): nginx-filesystem-1.20.1-14.el9_2.1.noarch.rpm                                                                         30 kB/s | 8.5 kB     00:00
(2/4): nginx-1.20.1-14.el9_2.1.x86_64.rpm                                                                                   120 kB/s |  36 kB     00:00
(3/4): rocky-logos-httpd-90.14-1.el9.noarch.rpm                                                                              70 kB/s |  24 kB     00:00
(4/4): nginx-core-1.20.1-14.el9_2.1.x86_64.rpm                                                                              3.6 MB/s | 565 kB     00:00
------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                       1.0 MB/s | 634 kB     00:00
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                                    1/1
  Running scriptlet: nginx-filesystem-1:1.20.1-14.el9_2.1.noarch                                                                                        1/4
  Installing       : nginx-filesystem-1:1.20.1-14.el9_2.1.noarch                                                                                        1/4
  Installing       : nginx-core-1:1.20.1-14.el9_2.1.x86_64                                                                                              2/4
  Installing       : rocky-logos-httpd-90.14-1.el9.noarch                                                                                               3/4
  Installing       : nginx-1:1.20.1-14.el9_2.1.x86_64                                                                                                   4/4
  Running scriptlet: nginx-1:1.20.1-14.el9_2.1.x86_64                                                                                                   4/4
  Verifying        : rocky-logos-httpd-90.14-1.el9.noarch                                                                                               1/4
  Verifying        : nginx-filesystem-1:1.20.1-14.el9_2.1.noarch                                                                                        2/4
  Verifying        : nginx-1:1.20.1-14.el9_2.1.x86_64                                                                                                   3/4
  Verifying        : nginx-core-1:1.20.1-14.el9_2.1.x86_64                                                                                              4/4

Installed:
  nginx-1:1.20.1-14.el9_2.1.x86_64 nginx-core-1:1.20.1-14.el9_2.1.x86_64 nginx-filesystem-1:1.20.1-14.el9_2.1.noarch rocky-logos-httpd-90.14-1.el9.noarch

Complete!
```
```
[web.lan2.tp2@localhost ~]$ sudo firewall-cmd --add-port=80/tcp --permanent
success
```
```
[web.lan2.tp2@localhost ~]$ sudo firewall-cmd --reload
success
```
```
[web.lan2.tp2@localhost ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 80/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```
```
[web.lan2.tp2@localhost ~]$ sudo systemctl start nginx
```
```
[web.lan2.tp2@localhost ~]$ sudo systemctl enable nginx
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.
```
```
[web.lan2.tp2@localhost ~]$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: disabled)
     Active: active (running) since Mon 2023-11-06 03:25:37 CET; 4min 17s ago
   Main PID: 11440 (nginx)
      Tasks: 3 (limit: 11127)
     Memory: 2.8M
        CPU: 28ms
     CGroup: /system.slice/nginx.service
             ├─11440 "nginx: master process /usr/sbin/nginx"
             ├─11441 "nginx: worker process"
             └─11442 "nginx: worker process"

Nov 06 03:25:37 localhost.localdomain systemd[1]: Starting The nginx HTTP and reverse proxy server...
Nov 06 03:25:37 localhost.localdomain nginx[11438]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Nov 06 03:25:37 localhost.localdomain nginx[11438]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Nov 06 03:25:37 localhost.localdomain systemd[1]: Started The nginx HTTP and reverse proxy server.
```
```
[web.lan2.tp2@localhost ~]$ sudo ss -alnpt | grep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=11442,fd=6),("nginx",pid=11441,fd=6),("nginx",pid=11440,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=11442,fd=7),("nginx",pid=11441,fd=7),("nginx",pid=11440,fd=7))
```
```
[web.lan2.tp2@localhost ~]$ sudo mkdir -p /var/www/site_nul
```
```
[web.lan2.tp2@localhost ~]$ cat /var/www/site_nul/index.html
<html>
        <head>
                <title>Welcome to your_domain</title>
        </head>
        <body>
                <h1>Success! Your Nginx server is successfully configured for <em>your_domain</em>. </h1>
                <p>This is a sample page.</p>
        </body>
</html>
```
- éditez le fichier `hosts` pour que `site_nul.tp2` pointe vers l'IP de `web.lan2.tp2`
```
[node1.lan1.tp2@localhost ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.1.1.100 node1.lan1.tp2
10.1.1.253 dhcp.lan1.tp2
10.1.2.11 node1.lan2.tp2
10.1.2.12 web.lan2.tp2 site_nul.tp2
10.1.2.254 router.tp2
```
- visitez le site nul avec une commande `curl` et en utilisant le nom `site_nul.tp2`

```
[node1.lan1.tp2@localhost ~]$ curl site_nul.tp2
<html>
    <head>
        <title>Welcome to your_domain</title>
    </head>
    <body>
        <h1>Success! Your Nginx server is successfully configured for <em>your_domain</em>. </h1>
<p>This is a sample page.</p>
    </body>
</html>
```