# VPN

## Задание
1. Между двумя виртуалками поднять vpn в режимах  
- tun  
- tap  
2. Поднять RAS на базе OpenVPN с клиентскими сертификатами, подключиться с локальной машины на виртуалку  

## Настройка стенда

автоматически настраивается плэйбуком с машины provision  

## VPN в режимах tun/tap
Результаты тестов пропускной способности  
В режиме tun  
```bash
[vagrant@client ~]$ iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  4] local 10.10.10.2 port 48160 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-5.00   sec  52.2 MBytes  87.6 Mbits/sec   29    276 KBytes       
[  4]   5.00-10.00  sec  53.5 MBytes  89.8 Mbits/sec    0    392 KBytes       
[  4]  10.00-15.00  sec  53.3 MBytes  89.5 Mbits/sec   25    411 KBytes       
[  4]  15.00-20.01  sec  54.3 MBytes  90.9 Mbits/sec   26    250 KBytes       
[  4]  20.01-25.00  sec  53.3 MBytes  89.5 Mbits/sec    0    374 KBytes       
[  4]  25.00-30.00  sec  54.2 MBytes  90.9 Mbits/sec    0    466 KBytes       
[  4]  30.00-35.00  sec  53.3 MBytes  89.5 Mbits/sec   25    345 KBytes       
[  4]  35.00-40.00  sec  54.5 MBytes  91.4 Mbits/sec    0    444 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-40.00  sec   429 MBytes  89.9 Mbits/sec  105             sender
[  4]   0.00-40.00  sec   427 MBytes  89.6 Mbits/sec                  receiver

iperf Done.
```
В режиме tap  
```bash
[vagrant@client ~]$ iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  4] local 10.10.10.2 port 39078 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-5.00   sec  50.9 MBytes  85.5 Mbits/sec   24    280 KBytes       
[  4]   5.00-10.01  sec  53.0 MBytes  88.8 Mbits/sec    0    391 KBytes       
[  4]  10.01-15.01  sec  51.6 MBytes  86.6 Mbits/sec   23    294 KBytes       
[  4]  15.01-20.00  sec  51.9 MBytes  87.3 Mbits/sec    0    400 KBytes       
[  4]  20.00-25.01  sec  51.9 MBytes  86.9 Mbits/sec   19    400 KBytes       
[  4]  25.01-30.00  sec  51.9 MBytes  87.2 Mbits/sec   16    379 KBytes       
[  4]  30.00-35.00  sec  52.3 MBytes  87.8 Mbits/sec    0    467 KBytes       
[  4]  35.00-40.00  sec  51.7 MBytes  86.8 Mbits/sec   27    341 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-40.00  sec   415 MBytes  87.1 Mbits/sec  109             sender
[  4]   0.00-40.00  sec   414 MBytes  86.8 Mbits/sec                  receiver

iperf Done.

```

В сравнении видно, что tap медленнее чем tun вследствие поддержки L2


## VPN с сертификатами
Подключаюсь с хоста к серверу  
```bash
[root@centos7 linux-dz-19]# openvpn --config client.conf
Wed Apr 15 16:20:54 2020 WARNING: file './client.key' is group or others accessible
Wed Apr 15 16:20:54 2020 OpenVPN 2.4.8 x86_64-redhat-linux-gnu [Fedora EPEL patched] [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Nov  1 2019
Wed Apr 15 16:20:54 2020 library versions: OpenSSL 1.0.2k-fips  26 Jan 2017, LZO 2.06
Wed Apr 15 16:20:54 2020 WARNING: No server certificate verification method has been enabled.  See http://openvpn.net/howto.html#mitm for more info.
Wed Apr 15 16:20:54 2020 TCP/UDP: Preserving recently used remote address: [AF_INET]192.168.19.30:1207
Wed Apr 15 16:20:54 2020 Socket Buffers: R=[212992->212992] S=[212992->212992]
Wed Apr 15 16:20:54 2020 UDP link local (bound): [AF_INET][undef]:1194
Wed Apr 15 16:20:54 2020 UDP link remote: [AF_INET]192.168.19.30:1207
Wed Apr 15 16:20:54 2020 TLS: Initial packet from [AF_INET]192.168.19.30:1207, sid=7ec8eefc c9fe2b23
Wed Apr 15 16:20:54 2020 VERIFY OK: depth=1, CN=rasvpn
Wed Apr 15 16:20:54 2020 VERIFY OK: depth=0, CN=rasvpn
Wed Apr 15 16:20:54 2020 Control Channel: TLSv1.2, cipher TLSv1/SSLv3 ECDHE-RSA-AES256-GCM-SHA384, 2048 bit RSA
Wed Apr 15 16:20:54 2020 [rasvpn] Peer Connection Initiated with [AF_INET]192.168.19.30:1207
Wed Apr 15 16:20:56 2020 SENT CONTROL [rasvpn]: 'PUSH_REQUEST' (status=1)
Wed Apr 15 16:20:56 2020 PUSH: Received control message: 'PUSH_REPLY,route 192.168.10.0 255.255.255.0,route 10.10.10.0 255.255.255.0,topology net30,ping 10,ping-restart 120,ifconfig 10.10.10.6 10.10.10.5,peer-id 0,cipher AES-256-GCM'
...
```
Проверяю доступность и маршруты к сетям сервера на хосте  
```bash
[root@centos7 ~]# ping -c 4 10.10.10.1
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=0.474 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=64 time=0.482 ms
64 bytes from 10.10.10.1: icmp_seq=3 ttl=64 time=0.424 ms
64 bytes from 10.10.10.1: icmp_seq=4 ttl=64 time=0.466 ms

--- 10.10.10.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3000ms
rtt min/avg/max/mdev = 0.424/0.461/0.482/0.031 ms
```
```bash
[root@centos7 ~]# ip r
... 
10.10.10.0/24 via 10.10.10.5 dev tun0 
10.10.10.5 dev tun0 proto kernel scope link src 10.10.10.6 
192.168.10.0/24 via 10.10.10.5 dev tun0 
... 

```

