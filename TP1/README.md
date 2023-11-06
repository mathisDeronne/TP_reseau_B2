# TP1 : Maîtrise réseau du poste

Déterminer...

- l'adresse MAC de votre carte WiFi

```
PS C:\Users\mathi> ipconfig /all

[...]
Carte réseau sans fil Wi-Fi :

   Adresse IPv6 de liaison locale. . . . .: fe80::ec24:eb3c:6d99:f344%14(préféré)
```

- l'adresse IP de votre carte WiFi

```
PS C:\Users\mathi> ipconfig /all

   [...]
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.76.183(préféré)
```
- le masque de sous-réseau du réseau LAN auquel vous êtes connectés en WiFi
  - en notation CIDR, par exemple `/16`
  ```
  /20
  ```
  - ET en notation décimale, par exemple `255.255.0.0`
```
PS C:\Users\mathi> ipconfig /all

[...]
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
```

- l'adresse de réseau du LAN auquel vous êtes connectés en WiFi

```
10.33.76.0
```
- l'adresse de broadcast
```
10.33.76.255
```
- le nombre d'adresses IP disponibles dans ce réseau
```
254
```
- déterminer le hostname de votre PC
```
PS C:\Users\mathi> hostname
DELLG3
```

- l'adresse IP de la passerelle du réseau
```
PS C:\Users\mathi> ipconfig /all
[...]
Carte réseau sans fil Wi-Fi :
[...]
Passerelle par défaut. . . . . . . . . : 10.33.79.254
```
- l'adresse MAC de la passerelle du réseau
```
PS C:\Users\mathi> arp -a
  Adresse Internet      Adresse physique
[...]
  10.33.79.254          7c-5a-1c-d3-d8-76
```
- l'adresse IP du serveur DHCP qui vous a filé une IP
```
PS C:\Users\mathi> ipconfig /all
[...]
Carte réseau sans fil Wi-Fi :
[...]
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
```
- l'adresse IP du serveur DNS que vous utilisez quand vous allez sur internet
```
PS C:\Users\mathi> ipconfig /all
[...]
Carte réseau sans fil Wi-Fi :
[...]
  Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
```
- dans votre table de routage, laquelle est la route par défaut
```
PS C:\Users\mathi> route print -4
[...]
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
          0.0.0.0          0.0.0.0     10.33.79.254     10.33.76.183     30
```

- faites en sorte que pour votre PC, le nom `b2.hello.vous` corresponde à l'IP `1.1.1.1`
```
PS C:\Users\mathi> cat C:\Windows\System32\drivers\etc\hosts
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#       127.0.0.1       localhost
#       ::1             localhost
        1.1.1.1         b2.hello.vous
```

- prouvez avec un `ping b2.hello.vous` que ça ping bien `1.1.1.1`
```
PS C:\Users\mathi> ping b2.hello.vous

Envoi d’une requête 'ping' sur b2.hello.vous [1.1.1.1] avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=10 ms TTL=57
Réponse de 1.1.1.1 : octets=32 temps=10 ms TTL=57
Réponse de 1.1.1.1 : octets=32 temps=10 ms TTL=57
Réponse de 1.1.1.1 : octets=32 temps=11 ms TTL=57

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 10ms, Maximum = 11ms, Moyenne = 10ms
```

- l'adresse IP du serveur auquel vous êtes connectés pour regarder la vidéo
- le port du serveur auquel vous êtes connectés
- le port que votre PC a ouvert en local pour se connecter au port du serveur distant
```
PS C:\Users\mathi> netstat -ano | Select-String 142.250.179.98

  TCP    10.33.76.183:56141     142.250.179.78:443
```
Déterminer...

- à quelle adresse IP correspond le nom de domaine `www.ynov.com`

```
PS C:\Users\mathi> nslookup www.ynov.com
Serveur :   GEN8
Address:  192.168.1.1

Réponse ne faisant pas autorité :
Nom :    www.ynov.com
Addresses:  2606:4700:20::681a:ae9
          2606:4700:20::681a:be9
          2606:4700:20::ac43:4ae2
          104.26.10.233
          104.26.11.233
          172.67.74.226
```
- à quel nom de domaine correspond l'IP `174.43.238.89` 
```
PS C:\Users\mathi> nslookup 174.43.238.89
Serveur :   GEN8
Address:  192.168.1.1

Nom :    89.sub-174-43-238.myvzw.com
Address:  174.43.238.89
```
Déterminer...

- par combien de machines vos paquets passent quand vous essayez de joindre `www.ynov.com`

```
PS C:\Users\mathi> tracert www.ynov.com

Détermination de l’itinéraire vers www.ynov.com [2606:4700:20::ac43:4ae2]
avec un maximum de 30 sauts :

  1     1 ms    <1 ms     1 ms  2a02-842b-f660-0201-ce19-a8ff-fe46-319f.rev.sfr.net [2a02:842b:f660:201:ce19:a8ff:fe46:319f]
  2     *        *        *     Délai d’attente de la demande dépassé.
  3     4 ms     2 ms     2 ms  2a02-8400-0000-0003-0000-0000-0000-62aa.rev.sfr.net [2a02:8400:0:3::62aa]
  4     *        *        *     Délai d’attente de la demande dépassé.
  5     *        *        *     Délai d’attente de la demande dépassé.
  6     *        *        *     Délai d’attente de la demande dépassé.
  7     *        *        *     Délai d’attente de la demande dépassé.
  8    13 ms    11 ms    10 ms  2400:cb00:19:200::48
  9    17 ms    15 ms    16 ms  2400:cb00:538:3::
 10    10 ms    10 ms    10 ms  2606:4700:20::ac43:4ae2

Itinéraire déterminé.
```
Déterminer...

- l'adresse IP publique de la passerelle du réseau (le routeur d'YNOV donc si vous êtes dans les locaux d'YNOV quand vous faites le TP)
```
PS C:\Users\mathi> curl ipconfig.me


StatusCode        : 200
StatusDescription : OK
Content           : 2a02:842b:f660:201:394d:4723:9849:7e55
RawContent        : HTTP/1.1 200 OK
                    access-control-allow-origin: *
                    x-envoy-upstream-service-time: 0
                    strict-transport-security: max-age=2592000; includeSubDomains
                    Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=259200...
Forms             : {}
Headers           : {[access-control-allow-origin, *], [x-envoy-upstream-service-time, 0], [strict-transport-security, max-age=2592000;
                    includeSubDomains], [Alt-Svc, h3=":443"; ma=2592000,h3-29=":443"; ma=2592000]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : System.__ComObject
RawContentLength  : 38
```

