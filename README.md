# Jarkom-Modul-3-F04-2022

# Anggota
Nama | NRP | Kontribusi |
--- | --- | --- |
Hilmi Zharfan Rachmadi | 5025201268 |  |
Ida Bagus Kade Rainata Putra Wibawa | 5025201235 | Mengerjakan soal no 3-8 |
Naufal Faadhilah | 5025201221 |  |

# Jawaban
## Soal 1

### Topologi dan Konfigurasi

### Topologi dan Konfigurasi

<img src="https://user-images.githubusercontent.com/70790033/201673879-0dcfcf52-5d9c-4c33-8209-d036f5bc97f5.png" width="500">

#### Konfigurasi Ostania
```bash
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.201.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.201.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.201.3.1
	netmask 255.255.255.0
```

#### Konfigurasi WISE
```bash
auto eth0
iface eth0 inet static
       address 192.201.2.2
       netmask 255.255.255.0
       gateway 192.201.2.1 
```

#### Konfigurasi Berlint
```bash
auto eth0
iface eth0 inet static
       address 192.201.2.3
       netmask 255.255.255.0
       gateway 192.201.2.1
```

#### Konfigurasi Westalis
```bash
auto eth0
iface eth0 inet static
	address 192.201.2.4
	netmask 255.255.255.0
	gateway 192.201.2.1
```

#### Konfigurasi Eden
```bash
auto eth0
iface eth0 inet dhcp
hwaddress ether 12:34:56:78:9a:bc
```

#### Konfigurasi NewstonCastle
```bash
auto eth0
iface eth0 inet dhcp
```

#### Konfigurasi KemonoPark
```bash
auto eth0
iface eth0 inet dhcp
```

#### Konfigurasi SSS
```bash
auto eth0
iface eth0 inet dhcp
```

#### Konfigurasi Garden
```bash
auto eth0
iface eth0 inet dhcp
```

### DNS Server
Yang berperan sebagai DNS Server adalah WISE. 

#### Instalasi bind9
```bash
apt-get update
apt-get upgrade
apt-get install bind9 -y
```

#### Konfigurasi
```bash
options {
        directory \"/var/cache/bind\";
        forwarders {
                192.168.122.1;
        };
        allow-query{any;};
        auth-nxdomain no;
        listen-on-v6 { any; };
};
```

Diakhiri dengan restart bind9
```bash
service bind9 restart 
service bind9 status
```

### Menghubungkan dengan Internet

#### Pada Ostania
```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.201.0.0/16
```

#### Pada WISE, Berlint, Westalis
```bash
echo "nameserver 192.168.122.1" > /etc/resolv.conf
```

### DHCP Server
Yang berperan sebagai DHCP Server adalah Westalis.

#### Instalasi DHCP Server
```bash
apt-get update
apt-get upgrade
apt-get install isc-dhcp-server -y
```

#### Konfigurasi DHCP Server
Memilih interface eth0 sesuai topologi
```bash
echo "INTERFACES=\"eth0\"" > /etc/default/isc-dhcp-server
```
Konfigurasi isc-dhcp-server dalam file /etc/dhcp/dhcpd.conf
```bash
subnet 192.201.2.0 netmask 255.255.255.0 {
    range 192.201.2.7 192.201.2.30; #Random range (mengikuti modul)
    option routers 192.201.2.1;
    option broadcast-address 192.201.2.255;
    option domain-name-servers 192.201.2.2;
    default-lease-time 600; #Random Lease Time (mengikuti modul)
    max-lease-time 7200;
 ```

### Proxy Server
Yang berperan sebagai Proxy Server adalah Berlint.

#### Instalasi Squid
```bash
apt-get update
apt-get upgrade
apt-get install squid -y
service squid status
```

## Soal 2
Ostania dijadikan DHCP Relay

### Install DHCP Relay
```bash
apt-get update
apt-get upgrade
apt-get install isc-dhcp-relay -y
```

### Konfigurasi
Terletak di /etc/default/isc-dhcp-relay
```bash
# Server yang dituju oleh DHCP Relay (DHCP Server yaitu Westalis)
SERVERS=\"192.201.2.4\"
# Interface mana saja layanan DHCP Relay melayani DHCP Request
INTERFACES=\"eth1 eth2 eth3\"
# Opsional
OPTIONS=\"\"
````

### Memeriksa Status DHCP Relay
```bash
service isc-dhcp-relay restart
service isc-dhcp-relay status
echo "BERHASIL"
```

## Soal 3
Mengatur range IP di /etc/dhcp/dhcpd.conf untuk client yang melalui Switch1. Pengaturan dilakukan dari Westalis yang merupakan DHCP Server. 
```bash
subnet 192.201.1.0 netmask 255.255.255.0 {
    range 192.201.1.50 192.201.1.88; 
    range 192.201.1.120 192.201.1.155;
    option routers 192.201.1.1;
    option broadcast-address 192.201.1.255;
    option domain-name-servers 192.201.2.2;
```

Memeriksa IP di client SSS yang terhubung melalui Switch 1

<img src="https://user-images.githubusercontent.com/70790033/201709852-488e52d2-8902-4b1a-a92b-69550900fcf2.png" width="500">

Terlihat bahwa alamat IP-nya adalah 192.201.1.53 yang masuk ke dalam range 192.201.1.50 hingga 192.201.1.88

## Soal 4
Mengatur range IP di /etc/dhcp/dhcpd.conf untuk client yang melalui Switch3. Pengaturan dilakukan dari Westalis yang merupakan DHCP Server. 
```bash
subnet 192.201.3.0 netmask 255.255.255.0 {
    range 192.201.3.10 192.201.3.30; 
    range 192.201.3.60 192.201.3.85;
    option routers 192.201.3.1;
    option broadcast-address 192.201.3.255;
    option domain-name-servers 192.201.2.2; # ref ke WISE
```

Memeriksa IP di client NewstonCastle yang terhubung melalui Switch 3

<img src="https://user-images.githubusercontent.com/70790033/201710299-3cd03692-19c5-4db4-9ac3-3a1007bb3873.png" width="500">

Terlihat bahwa alamat IP-nya adalah 192.201.3.13 yang masuk ke dalam range 192.201.3.10 hingga 192.201.3.30

## Soal 5
### Menghubungkan client ke internet melalui WISE
<img src="https://user-images.githubusercontent.com/70790033/201711097-3062fb53-0966-4ec9-b423-ed2f18c805f3.png" width="500">
<img src="https://user-images.githubusercontent.com/70790033/201711400-750a9be9-ebf5-4aed-8469-0e5dc3dd9af6.png" width="500">

Terlihat bahwa client seperti Garden dan KemonoPark dapat mengakses internet.

## Soal 6
Menambahkan default-lease-time dan max-lease-time ke /etc/dhcp/dhcpd.conf.

### Switch 1
```bash
subnet 192.201.1.0 netmask 255.255.255.0 {
    range 192.201.1.50 192.201.1.88; 
    range 192.201.1.120 192.201.1.155;
    option routers 192.201.1.1;
    option broadcast-address 192.201.1.255;
    option domain-name-servers 192.201.2.2;
    default-lease-time 300; # 5 menit 
    max-lease-time 6900; # 115 menit 
```
### Switch 3
```bash
subnet 192.201.3.0 netmask 255.255.255.0 {
    range 192.201.3.10 192.201.3.30; 
    range 192.201.3.60 192.201.3.85;
    option routers 192.201.3.1;
    option broadcast-address 192.201.3.255;
    option domain-name-servers 192.201.2.2; # ref ke WISE
    default-lease-time 600; # 10 menit 
    max-lease-time 6900; # 115 menit
```

## Soal 7
Memberikan alamat IP yang tetap untuk Eden

### Dari Westalis
Menambahkan konfigurasi ke /etc/dhcp/dhcpd.conf berupa
```bash
host Eden {
hardware ethernet 12:34:56:78:9a:bc;
fixed-address 192.201.3.13;
}
```

Untuk memeriksa apakah IP sudah tetap, dapat dijalankan command ```ip a``` di Eden.

<img src="https://user-images.githubusercontent.com/70790033/201710666-61b69cf4-13ab-4236-9ec2-f6a1986aacae.png" width="500">

## Soal 8
Menambahkan konfigurasi waktu akses client di Squid, melalui file /etc/squid/acl.conf dan /etc/squid/squid.conf
```bash
acl AVAILABLE_WORKING time MTWHF 17:01-23:59
acl AVAILABLE_WORKING time MTWHF 00:00-07:59
acl AVAILABLE_WORKING time AS 00:00-23:59 
acl working_hour time MTWHF 08:00-17:00
acl loid_work dstdomain loid-work.com
acl franky_work dstdomain franky-work.com
```

```bash
include /etc/squid/acl.conf
http_port 8080
http_access allow AVAILABLE_WORKING
http_access allow loid_work working_hour
http_access allow franky_work working_hour 
http_access deny all
visible_hostname Berlint
```

## Catatan Script
- Ostania - ```ostania1.sh```
- WISE - ```wise.sh```
- Berlint - ```berlint.sh```
- Westalis - ```westalis.sh```
- Eden - ```eden.sh```

## Kendala
- Teman saya Naufal Faadhilah tidak dapat menjalankan command `ping google.com` dari Ostania tanpa sebab yang diketahui.

