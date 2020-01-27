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

#### Unshare

<p>On peut créer des namespaces avec l'appel système unshare(). Il existe une commande unshare qui nous permet de l'utiliser directement.</p>
🌞 Créer un pseudo-conteneur à la main en utilisant unshare
