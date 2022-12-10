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
#### Strix
Agar IP Strix tidak berubah-ubah, pada Strix dibuat fix address dengan memasukkan <code>hwaddress</code>.
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
hwaddress ether b6:23:6b:b5:5d:33

auto eth1
iface eth1 inet static
address 10.28.7.149
netmask 255.255.255.252

auto eth2
iface eth2 inet static
address 10.28.7.145
netmask 255.255.255.252
```

#### Ostania
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.28.7.150
netmask 255.255.255.252
gateway 10.28.7.149

auto eth1
iface eth1 inet static
address 10.28.4.1
netmask 255.255.254.0

auto eth2
iface eth2 inet static
address 10.28.7.137
netmask 255.255.255.248

auto eth3
iface eth3 inet static
address 10.28.6.1
netmask 255.255.255.0
```

### Westalis
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.28.7.146
netmask 255.255.255.252
gateway 10.28.7.145

auto eth1
iface eth1 inet static
address 10.28.0.1
netmask 255.255.252.0

auto eth2
iface eth2 inet static
address 10.28.7.1
netmask 255.255.255.128

auto eth3
iface eth3 inet static
address 10.28.7.129
netmask 255.255.255.248
```

#### Garden
```
auto eth0
iface eth0 inet static
address 10.28.7.138
netmask 255.255.255.248
gateway 10.28.7.137
```

#### SSS
```
auto eth0
iface eth0 inet static
address 10.28.7.139
netmask 255.255.255.248
gateway 10.28.7.137
```

#### Eden
```
auto eth0
iface eth0 inet static
address 10.28.7.130
netmask 255.255.255.248
gateway 10.28.7.129
```

#### WISE
```
auto eth0
iface eth0 inet static
address 10.8.7.131
netmask 255.255.255.248
gateway 10.8.7.129
```

#### Client: BlackBell, Briar, Desmond, dan Forger
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
```

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

### D. Konfigurasi DNS, Web server, DHCP Server, dan DHCP relay
#### Konfigurasi DNS Server
Eden sebagai DNS Server, maka pada Eden ditambahkan konfigurasi pada file <code>/etc/bind/named.conf.options</code>
```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        forwarders {
                192.168.122.1;
        };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

#### Konfigurasi DHCP Server
WISE sebagai DHCP Server, maka pada WISE ditambahkan file-file sebagai berikut:
- <code>.bashrc</code>
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```
- <code>/etc/default/isc-dhcp-server</code>
```
# Defaults for isc-dhcp-server initscript
# sourced by /etc/init.d/isc-dhcp-server
# installed at /etc/default/isc-dhcp-server by the maintainer scripts

#
# This is a POSIX shell fragment
#

# Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPD_CONF=/etc/dhcp/dhcpd.conf

# Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPD_PID=/var/run/dhcpd.pid

# Additional options to start dhcpd with.
#       Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACES="eth0"
```

- <code>/etc/dhcp/dhcpd.conf</code>
```
#Forger
subnet 10.28.7.0 netmask 255.255.255.128 {
    range 10.28.7.2 10.8.7.126;
    option routers 10.28.7.1;
    option broadcast-address 10.28.7.127;
    option domain-name-servers 10.28.7.130; 
    default-lease-time 600;
    max-lease-time 7200;
}

#Desmond
subnet 10.28.0.0 netmask 255.255.252.0 {
    range 10.28.0.2 10.28.3.254;
    option routers 10.28.0.1;
    option broadcast-address 10.28.3.255;
    option domain-name-servers 10.28.7.130;
    default-lease-time 600;
    max-lease-time 7200;
}

#Blackbell
subnet 10.28.4.0 netmask 255.255.254.0 {
    range 10.28.4.2 10.28.5.254;
    option routers 10.28.4.1;
    option broadcast-address 10.28.5.255;
    option domain-name-servers 10.28.7.130;
    default-lease-time 600;
    max-lease-time 7200;
}

#Briar
subnet 10.28.6.0 netmask 255.255.255.0 {
    range 10.28.6.2 10.8.6.254;
    option routers 10.28.6.1;
    option broadcast-address 10.28.6.255;
    option domain-name-servers 10.28.7.130;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 10.28.7.128 netmask 255.255.255.248 {
    option routers 10.28.7.129;
}
```

#### Konfigurasi DHCP Relay
Ostania dan Westalis  sebagai DHCP Relay
- <code>.bashrc</code>
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
echo "" | apt-get install isc-dhcp-relay -y
```

- <code>/etc/default/isc-dhcp-relay</code>
```
# What servers should the DHCP relay forward requests to?
SERVERS="10.28.7.131"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth0 eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

#### Konfigurasi Web Server
Garden dan SSS sebagai Web Server

- <code>.bashrc</code>
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install apache2 -y
```

- <code>/var/www/html/index.html</code>
```
# Garden
Hello, Garden!

# SSS
Hello, SSS!
```

- <code>/etc/apache2/ports.conf</code>
```
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 443

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

#### Penyelesaian

1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.

Pertama, dapatkan terlebih dahulu IP dari Strix yang terhubung dengan NAT dengan menggunakan command ```ip a ```
![image](https://user-images.githubusercontent.com/94664966/206847269-9a37745f-c63d-4d13-b6bd-ffe763a455f3.png)

Karena diminta menggunakan ```MASQUERADE```, maka digunakan ```SNAT```. Source akan diubah dari yang awalnya ```0.0``` ke Strix dengan ```to-source 192.168.122.213``` 
```
iptables -t nat -A POSTROUTING -s 10.28.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.213
```
Hasil Testing
![image](https://user-images.githubusercontent.com/94664966/206849125-3da20d49-9cb6-4113-b11f-ebd0bcb4a255.png)

2. Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.

Syntax Iptables yang dapat digunakan untuk masalah diatas adalah sebagai berikut.
Iptables ditempatkan pada WISE yang merupakan DHCP Server.

```
iptables -A FORWARD -d 10.28.7.128/29 -i eth0 -p tcp --dport 80 -j DROP
iptables -A FORWARD -d 10.28.7.128/29 -i eth0 -p tcp --dport 80 -j DROP
```
Maka semua yang berada pada udp dan tcp akan di drop.
Hasil Testing
![image](https://user-images.githubusercontent.com/94664966/206849522-38ed18bd-46c7-4872-b969-ee4efba7022b.png)

3. Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

Karena koneksinya ICMP maka akan digunakan flag `-p` dengan nilai `icmp`. Selain, itu akan digunakan limit maksimal 2 untuk akses koneksi secara bersamaan sehingga dapat menggunakan --`connlimit-above 2` dan menambahkan target `DROP` agar koneksi lainnya selain 2 koneksi tersebut ditolak.
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP

```
Hail Testing
- DHCP Server
![image](https://user-images.githubusercontent.com/94664966/206854722-da6638df-de36-4744-83b1-f9723182d53c.png)
![image](https://user-images.githubusercontent.com/94664966/206855020-0de1b079-cfb3-403f-8f83-1271b538461f.png)
![image](https://user-images.githubusercontent.com/94664966/206855155-3a693b57-dbcc-4fc3-a049-99007a19aa49.png)

- DNS Server
![image](https://user-images.githubusercontent.com/94664966/206855218-c1b10267-66cd-40d1-8e93-4b12d044fffa.png)
![image](https://user-images.githubusercontent.com/94664966/206855390-51460bda-46ea-4a34-aa4f-25f104d31100.png)
![image](https://user-images.githubusercontent.com/94664966/206855429-37449f7c-9ffb-47a6-9ad1-d663e26955f0.png)


4. Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.

Karena dibatasi waktu tertentu maka kita batasi menggunakan `-m time` dengan `--timestart` sebagai waktu mulai dapat diakses bernilai 07.00 dan `--timestop` sebagai waktu akhir dapat diakses bernilai 16.00, serta membatasi hari berlaku dengan `--weekdays` untuk hari Senin hingga Jumat. Terdapat flag `-j` yang menentukan kapan akses akan di `ACCEPT` atau `REJECT`.

Pada DNS Server (Node Eden)
```
iptables -A INPUT -s 10.28.7.136/29 -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 10.28.7.136/29 -j REJECT
```
Pada Web Server (Node Garden dan SSS)
```
iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -j REJECT
```
Hasil Testing
![image](https://user-images.githubusercontent.com/94664966/206855893-6b69002e-62a0-4499-afb7-145afc660fcf.png)

![image](https://user-images.githubusercontent.com/94664966/206855966-5b819f74-11be-4066-abd9-98e223963848.png)


5. Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.

Karena diminta untuk setiap request akan didistribusikan secara bergantian antara Garden dan SSS, maka akan dikonfigurasi untuk masing-masing node dengan port untuk request masing-masing node adalah 80 dan 443 dengan menggunakan `--dport`. Selain itu, akan dibatasi secara bergantian dengan menggunakan `--every 2` sehingga akan bergantian terdistribusinya dengan mengarahkan ke node lain dengan menggunakan `--to-destination`.
```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.28.7.138 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.8.7.138:80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.28.7.138 -j DNAT --to-destination 10.28.7.139:80
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.28.7.139 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.8.7.139:443
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.28.7.139 -j DNAT --to-destination 10.8.7.138:443
```
Hasil Testing
- Request Ke Garden
