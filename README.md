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

### D Konfigurasi DNS, Web server, DHCP Server, dan DHCP relay

1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.
```
IPETH0="$(ip -br a | grep eth0 | awk '{print $NF}' | cut -d'/' -f1)"
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source "$IPETH0" -s 10.28.0.0/21
```
2. Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.
```
iptables -A FORWARD -d 10.28.7.131 -i eth0 -p tcp --dport 80 -j DROP
iptables -A FORWARD -d 10.28.7.130 -i eth0 -p tcp --dport 80 -j DROP
```
3. Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
4. Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.
```
iptables -A INPUT -s 10.28.7.0/25 -m time --weekdays Sat,Sun -j REJECT
iptables -A INPUT -s 10.28.7.0/25 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 10.28.7.0/25 -m time --timestart 15:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
```
```
iptables -A INPUT -s 10.28.0.0/22 -m time --weekdays Sat,Sun -j REJECT
iptables -A INPUT -s 10.28.0.0/22 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 10.28.0.0/22 -m time --timestart 15:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
```
5. Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.
```
