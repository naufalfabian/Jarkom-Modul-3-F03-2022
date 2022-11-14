# Jarkom-Modul-3-F03-2022
Jarkom-Modul-3-F03-2022

### Kelompok F03

| **No** | **Nama**                   | **NRP**    | **Pembagian Pekerjaan** |
| ------ | -------------------------- | ---------- | ----------------------- |
| 1      | Naufal Fabian Wibowo    | 05111940000223 | Mengerjakan Lapres |
| 2      | Angela Oryza Prabowo          | 5025201022 | Mengerjakan nomor 1-11, Menambahkan Dokumentasi |
| 3      | Helmi Taqiyuddin | 5025201152 | Mengerjakan Lapres |

``` IP Kelompok F03 = 10.30``` 

**Topologi**


**Konfigurasi Node**

**Ostania**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.30.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.30.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.30.3.1
	netmask 255.255.255.0

```

**WISE**
```
auto eth0
iface eth0 inet static
	address 10.30.2.2
	netmask 255.255.255.0
	gateway 10.30.2.1
```

**Berlint**
```
auto eth0
iface eth0 inet static
	address 10.30.2.3
	netmask 255.255.255.0
	gateway 10.30.2.1
```

**Westalis**
```
auto eth0
iface eth0 inet static
	address 10.30.2.4
	netmask 255.255.255.0
	gateway 10.30.2.1

```


## 1
> Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server
**WISE**
```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
service bind9 start
```

**Berlint**
```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install squid -y
service squid start
```

**Westalis**
```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```

## 2
> dan Ostania sebagai DHCP Relay
```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.30.0.0/16
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
```

## 3
> Loid dan Franky menyusun peta tersebut dengan hati-hati dan teliti.

> Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.

> Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155

Pada Westalis lakukan konfigurasi berikut
```bash
echo "
subnet 10.30.1.0 netmask 255.255.255.0 {
    range  10.30.1.50 10.30.1.88;
    range  10.30.1.120 10.30.1.155;
    option routers 10.30.1.1;
    option broadcast-address 10.30.1.255;
    option domain-name-servers 10.30.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}" >> /etc/dhcp/dhcpd.conf
```

**SSS** 
    <br>
    <picture>
     <img alt="Kemono Park" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/4-4.png?raw=true">
    </picture>
    
**Garden**
<br>
    <picture>
     <img alt="Newston Castle" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/4-5.png?raw=true">
    </picture>



## 4
> Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

Pada Westalis lakukan konfigurasi berikut
```bash
echo "
subnet 10.30.3.0 netmask 255.255.255.0 {
    range  10.30.3.10 10.30.3.30;
    range  10.30.3.60 10.30.3.85;
    option routers 10.30.3.1;
    option broadcast-address 10.30.3.255;
    option domain-name-servers 10.30.2.2;
    default-lease-time 600;
    max-lease-time 6900;
}" >> /etc/dhcp/dhcpd.conf
```

Lalu untuk client yang menggunakan interface eth0 static, di ubah menjadi dhcp

**Kemono Park** 
    <br>
    <picture>
     <img alt="Kemono Park" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/4-1.png?raw=true">
    </picture>
    
**NewstonCastle**
<br>
    <picture>
     <img alt="Newston Castle" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/4-2.png?raw=true">
    </picture>
    
**Eden**
<br>
    <picture>
     <img alt="Eden" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/4-3.png?raw=true">
    </picture>

## 5
> Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut

Lakukan konfigurasi pada WISE
```bash
echo "
options {
        directory \"/var/cache/bind\";

        forwarders {
                8.8.8.8;
                8.8.8.4;
        };

        // dnssec-validation auto;
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};" > /etc/bind/named.conf.options
```

Lalu lakukan restart bind9 menggunakan `service bind9 restart` dan restart DHPCP server dan DHCP relay

**Kemono Park** 
    <br>
    <picture>
     <img alt="Kemono Park" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/5-3.png?raw=true">
    </picture>
    
**NewstonCastle**
<br>
    <picture>
     <img alt="Newston Castle" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/5-2.png?raw=true">
    </picture>
    
**Eden**
<br>
    <picture>
     <img alt="Eden" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/5-1.png?raw=true">
    </picture>

**SSS** 
    <br>
    <picture>
     <img alt="Kemono Park" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/5-4.png?raw=true">
    </picture>
    
**Garden**
<br>
    <picture>
     <img alt="Newston Castle" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/5-5.png?raw=true">
    </picture>

## 6
> Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit. 

mengganti konfigurasi waktu pada file `/etc/dhcp/dhcpd.conf `
```bash
default-lease-time 300;
max-lease-time 6900;
```


## 7
> Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13

Menjalankan config berikut
```bash
echo "
host Eden {
    hardware ethernet 36:66:13:83:3e:5f;
    fixed-address 10.30.3.13;
}" >> /etc/dhcp/dhcpd.conf

```
lalu menyetting config eden
```bash
echo "
auto eth0
iface eth0 inet dhcp
hwaddress ether 36:66:13:83:3e:5f " > /etc/network/interfaces
```
**Eden**
<br>
    <picture>
     <img alt="Eden" src="https://github.com/naufalfabian/Jarkom-Modul-3-F03-2022/blob/main/dokumetasi/6.png?raw=true">
    </picture>

## 8
> SSS, Garden, dan Eden digunakan sebagai client Proxy agar pertukaran informasi dapat terjamin keamanannya, juga untuk mencegah kebocoran data.
> Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy. Adapun kriteria pengaturannya adalah sebagai berikut:

> 1. Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)

Buat file pada folder squid ```/etc/squid/acl-time,conf```
``` acl WORK_HOUR time MTWHF 08:00-16:59
    acl WEEK_END time SA 00:00-23:59
 ```
 
 pada squid.conf tambahkan
 ```
 include /etc/squid/acl-time.conf

http_port 8080
dns_nameservers 10.30.2.2
 
acl WORK_HOUR_SITES dstdomain \"/etc/squid/working-hour-sites.acl\"
 
http_access allow !WORK_HOUR
http_access deny all
 
visible_hostname Berlint
```


> 2. Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)

Wise
Masukan konfigurasi berikut pada Wise sebgai DNS Server pada ```/etc/bind/named.conf.local```
```
zone \"loid-work.com\" {
        type master;
        file \"/etc/bind/wise/loid-work.com\";
};
zone \"franky-work.com\" {
        type master;
        file \"/etc/bind/wise/franky-work.com\";
};
```

dan konfigurasi tiap websitenya 
Loid pada ```/etc/bind/wise/loid-work.com```
```
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     loid-work.com. root.loid-work.com. (
                        2022102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      loid-work.com.
@               IN      A       10.30.2.2 ;IP Wise
```
Franky pada ```/etc/bind/wise/franky-work.com```
```
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     franky-work.com. root.franky-work.com. (
                        2022102501      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      franky-work.com.
@               IN      A       10.30.2.2 ;IP Wise
```

setting apache tiap websitenya
Loid pada ```/etc/apache2/sites-available/loid-work.com.conf```
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName loid-work.com
 
        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Franky pada ```/etc/apache2/sites-available/franky-work.com.conf```
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName franky-work.com
 
        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Berlint
Masukan konfigurasi berikut pada Wise sebgai DNS Server pada ```/etc/squid/working-hour-sites.acl```
```
loid-work.com
franky-work.com
```

tambahkan konfigurasi ```/etc/squid/squic.conf```
```
acl WORK_HOUR_SITES dstdomain \"/etc/squid/working-hour-sites.acl\"
http_access allow WORK_HOUR_SITES WORK_HOUR
```
> 3. Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)

Berlint
buat file konfigurasi pada ```/etc/squid/acl-port.conf```
```
acl HTTPS_PORT port 443
acl CONNECT method CONNECT
```

tambahkan konfigurasi pada ```squid.conf```
```
include /etc/squid/acl-port.conf

http_access deny !HTTPS_PORT
http_access deny CONNECT !HTTPS_PORT
```

> 4.Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)

Berlint
buat konfigurasi pada ```/etc/squid/acl-bandwith.conf```
```
delay_pools 1
delay_class 1 1
delay_access 1 allow all
delay_parameters 1 16000/16000
```

pada konfigurasi ```etc/squid/squid.conf```
```
include /etc/squid/acl-bandwidth.conf
```

lalu restart squid

> 5. Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

Berlint 
Tambahkan konfigurasi pada ```/etc/squid/acl-bandwith.conf```
```
delay_pools 1
delay_class 1 1
delay_access 1 allow WEEK_END
delay_parameters 1 16000/16000
```

tambahkan konfigurasi pada ```/etc/squid/acl-time.conf```
```
acl WORK_HOUR time MTWHF 08:00-16:59
acl WEEK_END time SA 00:00-23:59
```




