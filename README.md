# Namespaces

#### Exploration manuelle

<p>Les namespaces sont identifiés par un ID de 10 digits. On peut trouver les namespaces d'un processus donné en utilisant le pseudo-filesystem /proc. Plus précisément, on peut les trouver dans le répertoire dédié à ce processus : /proc/PID.</p>


~~~~~
[root@localhost tmp]# ls /proc/1/ns/ -al
total 0
dr-x--x--x. 2 root root 0 Jan 27 10:25 .
dr-xr-xr-x. 9 root root 0 Jan 27 09:16 ..
lrwxrwxrwx. 1 root root 0 Jan 27 10:25 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx. 1 root root 0 Jan 27 10:25 ipc -> 'ipc:[4026531839]'
lrwxrwxrwx. 1 root root 0 Jan 27 10:25 mnt -> 'mnt:[4026531840]'
lrwxrwxrwx. 1 root root 0 Jan 27 10:25 net -> 'net:[4026531992]'
lrwxrwxrwx. 1 root root 0 Jan 27 10:25 pid -> 'pid:[4026531836]'
lrwxrwxrwx. 1 root root 0 Jan 27 10:38 pid_for_children -> 'pid:[4026531836]'
lrwxrwxrwx. 1 root root 0 Jan 27 10:25 user -> 'user:[4026531837]'
lrwxrwxrwx. 1 root root 0 Jan 27 10:25 uts -> 'uts:[4026531838]'
~~~~~

----

#### Unshare

<p>On peut créer des namespaces avec l'appel système unshare(). Il existe une commande unshare qui nous permet de l'utiliser directement.</p>
🌞 Créer un pseudo-conteneur à la main en utilisant unshare

~~~
[root@localhost ~]# ps -edf | grep bash
root      1719  1718  0 11:09 pts/0    00:00:00 -bash
root      2113  1719  0 12:00 pts/0    00:00:00 -bash
root      2202  2113  0 12:07 pts/0    00:00:00 unshare -fp /bin/bash
root      2203  2202  0 12:07 pts/0    00:00:00 /bin/bash
root      2252  2203  0 12:11 pts/0    00:00:00 grep --color=auto bash
~~~

Prouver depuis votre bash isolé que ces namespaces sont bien mis en place
~~~
[root@localhost ~]# ps -edf | grep bash
root      1719  1718  0 11:09 pts/0    00:00:00 -bash
root      2113  1719  0 12:00 pts/0    00:00:00 -bash
root      2202  2113  0 12:07 pts/0    00:00:00 unshare -fp /bin/bash
root      2203  2202  0 12:07 pts/0    00:00:00 /bin/bash
root      2392  2391  0 12:25 pts/1    00:00:00 -bash
root      2437  2203  0 12:28 pts/0    00:00:00 unshare -fp /bin/bash
root      2438  2437  0 12:28 pts/0    00:00:00 /bin/bash
root      2469  2392  0 12:31 pts/1    00:00:00 grep --color=auto bash
[root@localhost ~]# 
~~~

~~~
[root@localhost ~]# ls -al /proc/1718/ns
total 0
dr-x--x--x. 2 root root 0 Jan 27 12:31 .
dr-xr-xr-x. 9 root root 0 Jan 27 11:09 ..
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 ipc -> 'ipc:[4026531839]'
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 mnt -> 'mnt:[4026531840]'
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 net -> 'net:[4026531992]'
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 pid -> 'pid:[4026531836]'
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 pid_for_children -> 'pid:[4026531836]'
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 user -> 'user:[4026531837]'
lrwxrwxrwx. 1 root root 0 Jan 27 12:31 uts -> 'uts:[4026531838]'
[root@localhost ~]# 
~~~
----
#### Avec docker

<p>Lancer un conteneur qui tourne en tâche de fond, sur un sleep </p>
🌞 Trouver dans quels namespaces ce conteneur s'exécute.

  
~~~
[root@localhost ~]# docker inspect -f '{{ .State.Pid }}' c11b677f953f
4228
[root@localhost ~]# ls -al /proc/4228/ns
total 0
dr-x--x--x. 2 root root 0 Jan 27 14:17 .
dr-xr-xr-x. 9 root root 0 Jan 27 14:17 ..
lrwxrwxrwx. 1 root root 0 Jan 27 14:18 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx. 1 root root 0 Jan 27 14:18 ipc -> 'ipc:[4026532228]'
lrwxrwxrwx. 1 root root 0 Jan 27 14:18 mnt -> 'mnt:[4026532226]'
lrwxrwxrwx. 1 root root 0 Jan 27 14:17 net -> 'net:[4026532231]'
lrwxrwxrwx. 1 root root 0 Jan 27 14:18 pid -> 'pid:[4026532229]'
lrwxrwxrwx. 1 root root 0 Jan 27 14:18 pid_for_children -> 'pid:[4026532229]'
lrwxrwxrwx. 1 root root 0 Jan 27 14:18 user -> 'user:[4026531837]'
lrwxrwxrwx. 1 root root 0 Jan 27 14:18 uts -> 'uts:[4026532227]'
~~~
----

#### Nsenter

~~~
[root@localhost ~]# nsenter -t 1833 -u -n
[root@d0717b136b28 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
5: eth0@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
[root@d0717b136b28 ~]# 
~~~
----

#### Et alors, les namespaces User ?

<p> Mettez en place la configuration nécessaire pour que Docker utilise les namespaces de type User. </p>

~~~

~~~


