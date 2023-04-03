# 27-DNS:
- взять стенд https://github.com/erlong15/vagrant-bind
- добавить еще один сервер client2
- завести в зоне dns.lab имена
- web1 - смотрит на клиент1
- web2 смотрит на клиент2
- завести еще одну зону newdns.lab
- завести в ней запись
- www - смотрит на обоих клиентов
- настроить split-dns
- клиент1 - видит обе зоны, но в зоне dns.lab только web1
- клиент2 видит только dns.lab
- настроить все без выключения selinux*

Все изменения для выполлнения ДЗ внесены в `playbook`. 
Заходим на `client` и проверяем условия ДЗ:
```bash
[root@client ~]# ping www.newdns.lab
PING www.newdns.lab (192.168.50.15) 56(84) bytes of data.
64 bytes from client (192.168.50.15): icmp_seq=1 ttl=64 time=0.029 ms
64 bytes from client (192.168.50.15): icmp_seq=2 ttl=64 time=0.025 ms
64 bytes from client (192.168.50.15): icmp_seq=3 ttl=64 time=0.049 ms
64 bytes from client (192.168.50.15): icmp_seq=4 ttl=64 time=0.050 ms
64 bytes from client (192.168.50.15): icmp_seq=5 ttl=64 time=0.050 ms
^C
--- www.newdns.lab ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4002ms
rtt min/avg/max/mdev = 0.025/0.040/0.050/0.013 ms
[root@client ~]# ping web1.dns.lab
PING web1.dns.lab (192.168.50.15) 56(84) bytes of data.
64 bytes from client (192.168.50.15): icmp_seq=1 ttl=64 time=0.014 ms
64 bytes from client (192.168.50.15): icmp_seq=2 ttl=64 time=0.052 ms
64 bytes from client (192.168.50.15): icmp_seq=3 ttl=64 time=0.050 ms
^C
--- web1.dns.lab ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 0.014/0.038/0.052/0.018 ms
[root@client ~]# ping web2.dns.lab
ping: web2.dns.lab: Name or service not known
```
Клиент1  видит обе зоны (dns.lab и newdns.lab), однако информацию о хосте web2.dns.lab он получить не может.

```bash
[root@client2 ~]# ping www.newdns.lab
ping: www.newdns.lab: Name or service not known
[root@client2 ~]# ping web1.dns.lab
PING web1.dns.lab (192.168.50.15) 56(84) bytes of data.
64 bytes from 192.168.50.15 (192.168.50.15): icmp_seq=1 ttl=64 time=1.62 ms
64 bytes from 192.168.50.15 (192.168.50.15): icmp_seq=2 ttl=64 time=0.869 ms
64 bytes from 192.168.50.15 (192.168.50.15): icmp_seq=3 ttl=64 time=0.957 ms
^C
--- web1.dns.lab ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 0.869/1.149/1.623/0.338 ms
[root@client2 ~]# ping web2.dns.lab
PING web2.dns.lab (192.168.50.16) 56(84) bytes of data.
64 bytes from client2 (192.168.50.16): icmp_seq=1 ttl=64 time=0.017 ms
64 bytes from client2 (192.168.50.16): icmp_seq=2 ttl=64 time=0.043 ms
64 bytes from client2 (192.168.50.16): icmp_seq=3 ttl=64 time=0.058 ms
^C
--- web2.dns.lab ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2006ms
rtt min/avg/max/mdev = 0.017/0.039/0.058/0.017 ms
``` 
Клиент2 видит зону dns.lab, но не видит зону newdns.lab.


