# TP5 : Intégration
# I. Tester
🌞 **Essayer de lancer l'app**
- bah juste lance le !
```
[user@hosting python_app]$ file server.py
server.py: Python script, UTF-8 Unicode text executable
```
```
[user@hosting python_app]$ sudo python server.py
13337
Le serveur tourne sur 127.0.0.1:13337
```
- une commande `ss` qui me montre que le programme écoute derrière IP:port
```
[user@hosting ~]$ ss -alptn
State             Recv-Q            Send-Q                       Local Address:Port                         Peer Address:Port            Process
LISTEN            0                 1                                127.0.0.1:13337                             0.0.0.0:*
```

🌞 **Tester l'app depuis `hosting.tp5.b1`**

- maintenant, on lance le **client**
- et vous essayez d'utiliser l'application
```
[user@hosting python_app]$ sudo python server.py
[sudo] password for user:
13337
Le serveur tourne sur 127.0.0.1:13337
Un client ('127.0.0.1', 47700) s'est connecté.
Le client ('127.0.0.1', 47700) a envoyé 3+3
Réponse envoyée au client ('127.0.0.1', 47700) : 6.
```

```
[user@hosting python_app]$ sudo python client.py
[sudo] password for user:
Veuillez saisir une opération arithmétique : 3+3
```

# II. Intégrer
## 1. Environnement
🌞 **Créer un dossier /opt/calculatrice**

- il doit contenir le code de l'application

```
[user@hosting ~]$ sudo mkdir /opt/calculatrice
[user@hosting ~]$ cd b2-reseau-2023/tp/infra/5/python_app/
[user@hosting python_app]$ sudo cp server.py /opt/calculatrice/
[user@hosting python_app]$ sudo cp client.py /opt/calculatrice/
```
## 2. systemd service
🌞 **Créer le fichier `/etc/systemd/system/calculatrice.service`**
```
[user@hosting ~]$ sudo touch /etc/systemd/system/calculatrice.service
```
 - go faire un `ls -al` dans le dossier pour voir les permissions des autres fichiers `.service` déjà présents et faire pareil
```
[user@hosting system]$ ls -al
total 8
drwxr-xr-x. 9 root root 4096 Nov 30 15:03 .
drwxr-xr-x. 4 root root  166 Sep 29 09:59 ..
drwxr-xr-x. 2 root root   31 Oct 14  2022 basic.target.wants
-rw-r--r--. 1 root root    0 Nov 30 15:03 calculatrice.service
lrwxrwxrwx. 1 root root   37 Oct 14  2022 ctrl-alt-del.target -> /usr/lib/systemd/system/reboot.target
lrwxrwxrwx. 1 root root   41 Oct 14  2022 dbus-org.fedoraproject.FirewallD1.service -> /usr/lib/systemd/system/firewalld.service
lrwxrwxrwx. 1 root root   57 Oct 14  2022 dbus-org.freedesktop.nm-dispatcher.service -> /usr/lib/systemd/system/NetworkManager-dispatcher.service
lrwxrwxrwx. 1 root root   43 Oct 14  2022 dbus.service -> /usr/lib/systemd/system/dbus-broker.service
lrwxrwxrwx. 1 root root   41 Oct 14  2022 default.target -> /usr/lib/systemd/system/multi-user.target
drwxr-xr-x. 2 root root   32 Oct 14  2022 getty.target.wants
drwxr-xr-x. 2 root root 4096 Oct 14  2022 multi-user.target.wants
drwxr-xr-x. 2 root root   48 Oct 14  2022 network-online.target.wants
drwxr-xr-x. 2 root root   71 Oct 14  2022 sockets.target.wants
drwxr-xr-x. 2 root root  173 Oct 14  2022 sysinit.target.wants
drwxr-xr-x. 2 root root   56 Oct 14  2022 timers.target.wants
```
```
[user@hosting system]$ sudo chmod 777 calculatrice.service
```
- un contenu minimal serait :
```
[user@hosting system]$ cat calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py

[Install]
WantedBy=multi-user.target
```
➜ **Il est nécessaire** de taper la commande `systemctl daemon-reload` dès qu'on modifie un fichier `.service` pour indiquer à systemd de relire les fichiers et adopter la nouvelle configuration.
```
[user@hosting system]$ sudo systemctl daemon-reload
```
🌞 **Démarrer le service**
- avec une commande `sudo systemctl start calculatrice`
```
[user@hosting system]$ sudo systemctl start calculatrice
```
- prouvez que...
  - le service est actif avec un `systemctl status`
  ```
  ```