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

----
🌞 lancer un conteneur simple:

docker run -d -p 8888:7777 debian sleep 99999

~~~
[root@localhost toor]# docker run -d -p 8888:7777 debian sleep 99999
7822bed26a9e6312b2bcbeea5f508a8590cab3c8860b41a45a5f6b31b80032d1
[root@localhost toor]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
7822bed26a9e        debian              "sleep 99999"       3 seconds ago       Up 2 seconds        0.0.0.0:8888->7777/tcp   boring_ardinghelli
~~~

🌞 vérifier le réseau du conteneur

~~~
[root@localhost toor]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
7822bed26a9e        debian              "sleep 99999"       3 seconds ago       Up 2 seconds        0.0.0.0:8888->7777/tcp   boring_ardinghelli
[root@localhost toor]# docker exec -it boring_ardinghelli bash
root@7822bed26a9e:/# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
6: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
root@7822bed26a9e:/#
~~~
~~~
7: veth8adb334@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether e6:6b:ee:eb:99:79 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::e46b:eeff:feeb:9979/64 scope link
       valid_lft forever preferred_lft forever
~~~

🌞 vérifier le réseau sur l'hôte
vérifier qu'il existe une première carte réseau qui porte une IP dans le même réseau que le conteneur
~~~
 5: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:a3:cf:fc:1b brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:a3ff:fecf:fc1b/64 scope link
       valid_lft forever preferred_lft forever
~~~
vérifier qu'il existe une deuxième carte réseau, qui est la deuxième interface de la veth pair
~~~
7: veth8adb334@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether e6:6b:ee:eb:99:79 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::e46b:eeff:feeb:9979/64 scope link
       valid_lft forever preferred_lft forever
~~~
identifier les règles iptables liées à la création de votre conteneur
~~~
[root@localhost toor]# iptables --list
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
DOCKER-USER  all  --  anywhere             anywhere
DOCKER-ISOLATION-STAGE-1  all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
DOCKER     all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

Chain DOCKER (1 references)
target     prot opt source               destination
ACCEPT     tcp  --  anywhere             172.17.0.2           tcp dpt:cbt

Chain DOCKER-ISOLATION-STAGE-1 (1 references)
target     prot opt source               destination
DOCKER-ISOLATION-STAGE-2  all  --  anywhere             anywhere
RETURN     all  --  anywhere             anywhere

Chain DOCKER-ISOLATION-STAGE-2 (1 references)
target     prot opt source               destination
DROP       all  --  anywhere             anywhere
RETURN     all  --  anywhere             anywhere

Chain DOCKER-USER (1 references)
target     prot opt source               destination
RETURN     all  --  anywhere             anywhere
~~~  

# Cgroups

### Découverte manuelle

🌞 Lancer un conteneur Docker et déduire dans quel cgroup il s'exécute
~~~  

Control Group                                                                                                      Tasks   %CPU   Memory  Input/s Output/s
/                                                                                                                    138    1.0   698.9M        -        -
/system.slice                                                                                                         68    0.1   466.1M        -        -
/system.slice/containerd.service                                                                                      19    0.1    65.2M        -        -
/system.slice/tuned.service                                                                                            4    0.0    21.9M        -        -
/system.slice/docker.service                                                                                          15    0.0   125.6M        -        -
/system.slice/rsyslog.service                                                                                          3    0.0     3.9M        -        -
/docker                                                                                                                1      -     5.6M        -        -
/docker/7822bed26a9e6312b2bcbeea5f508a8590cab3c8860b41a45a5f6b31b80032d1
~~~  

### Utilisation par Docker

🌞 Lancer un conteneur Docker et trouver

~~~  
CONTAINER ID        NAME                 CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
7822bed26a9e        boring_ardinghelli   0.00%               720KiB / 1.787GiB   0.04%               2.5kB / 0B          10.9MB / 0B         1
~~~  
~~~
[root@localhost docker]# ls
7822bed26a9e6312b2bcbeea5f508a8590cab3c8860b41a45a5f6b31b80032d1  cpuacct.usage_all          cpuacct.usage_user  cpu.shares
cgroup.clone_children                                             cpuacct.usage_percpu       cpu.cfs_period_us   cpu.stat
cgroup.procs                                                      cpuacct.usage_percpu_sys   cpu.cfs_quota_us    notify_on_release
cpuacct.stat                                                      cpuacct.usage_percpu_user  cpu.rt_period_us    tasks
cpuacct.usage                                                     cpuacct.usage_sys          cpu.rt_runtime_us
[root@localhost docker]# cat cpu.shares
1024
~~~

🌞 Altérer les valeurs cgroups allouées par défaut avec des options de la commandes docker run (au moins 3)
~~~
[root@localhost docker]# docker run -d --memory=2g --memory-swap=-1 --cpus 1.00 -p 8888:7777 debian sleep 99999
cb79f23e690f9d2c5946d7992ea0a3963e6eccf37b5c0e468fea4e69875790a1
~~~
----
# Capabilities

### Découverte manuelle

🌞 déterminer les capabilities actuellement utilisées par votre shell
~~~
root@89ab7b9f3e0f:/# capsh --print
Current: = cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap+eip
Bounding set =cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap
Securebits: 00/0x0/1'b0
 secure-noroot: no (unlocked)
 secure-no-suid-fixup: no (unlocked)
 secure-keep-caps: no (unlocked)
uid=0(root)
gid=0(root)
groups=
~~~

🌞 Déterminer les capabilities du processus lancé par un conteneur Docker


