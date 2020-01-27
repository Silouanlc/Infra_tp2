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

