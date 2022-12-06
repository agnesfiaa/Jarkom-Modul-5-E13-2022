# Jarkom-Modul-5-E13-2022
- Rycahaya Sri Hutomo (5025201046)
- Agnesfia Anggraeni (5025201059)

## SOAL MODUL 5
(isi yaa ran)

## A. TOPOLOGI JARINGAN
[kasih ss yg udah ada lingkaran subnet]
<br>
### Tree pembagian IP menggunakan VLSM
[GAMBAR TREE]
<br>

### Tabel Pembagian IP
[SS spreadsheet tabel pembagian IP & NID]

### Konfigurasi Node

### Routing
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
