# Jarkom-Modul-5-E13-2022
- Rycahaya Sri Hutomo (5025201046)
- Agnesfia Anggraeni (5025201059)

## SOAL MODUL 5
Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Loid dibawah ini:

## A. TOPOLOGI JARINGAN
![VLSM-5-Subnet](https://user-images.githubusercontent.com/94664966/205910144-a64507df-0566-41d6-b09e-5e9262436a26.png)

<br>
Keterangan :	<br>
- Eden adalah DNS Server<br>
- WISE adalah DHCP Server<br>
- Garden dan SSS adalah Web Server<br>
- Jumlah Host pada Forger adalah 62 host<br>
- Jumlah Host pada Desmond adalah 700 host<br>
- Jumlah Host pada Blackbell adalah 255 host<br>
- Jumlah Host pada Briar adalah 200 host<br>

### B. Tree pembagian IP menggunakan VLSM
![VLSM-5-IP](https://user-images.githubusercontent.com/94664966/205911325-779c5c8c-8934-4ad6-8dd7-7ba8153d528c.png)
<br>

### Tabel Pembagian IP
![image](https://user-images.githubusercontent.com/94664966/205911587-301b04e2-3f01-46a7-a1db-459a1d2adbb0.png)

![image](https://user-images.githubusercontent.com/94664966/205911729-ffc0483b-d647-4443-89d2-431de6c913c0.png)


### Konfigurasi Node

### C. Routing
#### STRIX
```
route add -net 10.28.0.0 netmask 255.255.252.0 gw 10.28.7.146 #Desmond A2
route add -net 10.28.7.0 netmask 255.255.255.128 gw 10.28.7.146 #Forger A8
route add -net 10.28.7.128 netmask 255.255.255.248 gw 10.28.7.146 #Eden & WISE A1

route add -net 10.28.4.0 netmask 255.255.254.0 gw 10.28.7.150 #BlackBell A5
route add -net 10.28.6.0 netmask 255.255.255.0 gw 10.28.7.150 #Briar A6
route add -net 10.28.7.136 netmask 255.255.255.248 gw 10.28.7.150 #Garden & SSS
```

#### WESTALIS
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.28.7.145
```

#### OSTANIA
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.28.7.149
```

### Konfigurasi DNS, Web server, DHCP Server, dan DHCP relay
