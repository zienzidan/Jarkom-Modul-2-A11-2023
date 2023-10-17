# Jarkom-Modul-2-A11-2023
**Praktikum Jaringan Komputer Modul 2 Tahun 2023**

### Penulis
| Nama | NRP |Github |
|---------------------------|------------|--------|
|Muhammad Zien Zidan | 5025211122 | https://github.com/zienzidan |
|Glenaya | 5025211202 | https://github.com/nyawnayaw05 |

## Soal 1
> Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut.

Dalam soal ini, kami mendapatkan plottingan soal No. 3

![image](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/8c9be17f-2325-4a65-94b3-3a0970998de1)

## Configurasi
#### Pandudewanata
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.174.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.174.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.174.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.174.4.1
	netmask 255.255.255.0
```
#### Yudistira
```
auto eth0
iface eth0 inet static
	address 192.174.2.2
	netmask 255.255.255.0
	gateway 192.174.2.1
```
#### Werkudara
```
auto eth0
iface eth0 inet static
	address 192.174.1.2
	netmask 255.255.255.0
	gateway 192.174.1.1
```
#### Sadewa
```
auto eth0
iface eth0 inet static
	address 192.174.3.2
	netmask 255.255.255.0
	gateway 192.174.3.1
```
#### Nakula
```
auto eth0
iface eth0 inet static
	address 192.174.3.3
	netmask 255.255.255.0
	gateway 192.174.3.1
```
#### Arjuna-LB
```
auto eth0
iface eth0 inet static
	address 192.174.4.5
	netmask 255.255.255.0
	gateway 192.174.4.1
```
#### Prabukusuma
```
auto eth0
iface eth0 inet static
	address 192.174.4.2
	netmask 255.255.255.0
	gateway 192.174.4.1
```
#### Abimanyu
```
auto eth0
iface eth0 inet static
	address 192.174.4.3
	netmask 255.255.255.0
	gateway 192.174.4.1
```
####  Wissanggeni
```
auto eth0
iface eth0 inet static
	address 192.174.4.4
	netmask 255.255.255.0
	gateway 192.174.4.1
```

## Soal 2
> Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

Lakukan update dan instalasi terlebih dahulu menggunakan :
```shell
apt-get update
apt-get install bind9 -y
```

**Lalu set up pada DNS Master (Yudhistira)**
```
nano /etc/bind/named.conf.local
```

Lalu isi dengan :
```shell
zone "arjuna.a11.com" {
      type master;
      file "/etc/bind/jarkom/arjuna.a11.com";
```

Setelah itu buat folder jarkom ```mkdir /etc/bind/jarkom``` kemudian copy file db.local pada path /etc/bind ke dalam folder jarkom lalu ubah menjadi arjuna.a11.com 
```cp /etc/bind/db.local /etc/bind/jarkom/arjuna.a11.com```

Kemudian masuk ke file tersebut dengan ```nano /etc/bind/jarkom/arjuna.a11.com``` edit isinya seperti di bawah ini :
```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.a11.com. root.arjuna.a11.com. (
                        2023101101      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.a11.com.
@       IN      A       192.174.4.5    ; IP Arjuna
www     IN      CNAME   abimanyu.a11.com.
```

Lalu restart bind menggunakan ```service bind9 restart```

Kemudian pada clien (Sadewa atau Nakula) lakukan ```nano /etc/resolv.conf```. Kemudian ganti isinya dengan 
```shell 
nameserver 192.174.2.2 #IP Yudhistira
```

Lalu coba di test dengan cara ping menggunakan
```shell
ping arjuna.a11.com
ping www.arjuna.a11.com
```

**Hasil**

![image](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/6ceb33ca-4fcb-4ac7-8a64-a1769aa8aa6a)

## Soal 3
> Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

Caranya sama dengan nomor 2. Masuk pada direktori /etc/bind/named.conf.local dengan cara ```nano /etc/resolv.conf```. Lalu tambahkan
set up di bawah ini ke dalam file :

```shell
zone "abimanyu.a11.com" {
	type master;
	file "/etc/bind/jarkom/abimanyu.a11.com";
};
```

Kemudian copy file db.local pada path /etc/bind ke dalam folder jarkom lalu ubah menjadi abimanyu.a11.com 
```cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.a11.com```

Kemudian masuk ke file tersebut dengan ```nano /etc/bind/jarkom/abimanyu.a11.com``` edit isinya seperti di bawah ini :
```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.a11.com. root.abimanyu.a11.com. (
                        2023101101      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.a11.com.
@       IN      A       192.174.4.3     ; IP Abimanyu
www     IN      CNAME   abimanyu.a11.com.
```

Restart bind menggunakan ```service bind9 restart```

Lalu coba di test dengan cara ping di Nakula atau Sadewa menggunakan
```shell
ping abimanyu.a11.com
ping www.abimanyu.a11.com
```
**Hasil**
![image](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/491a2a3c-1dbd-4b97-8d98-eb9fc54189d4)

## Soal 4
> Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

Pada soal ini kita melakukan penambahan ``parikesit IN    A       192.174.4.3     ; IP Abimanyu``` 
pada file /etc/bind/abimanyu.a11.com yang terdapat pada DNS Mater yaitu Yudhistira

Isinya akan menjadi seperti ini :
```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.a11.com. root.abimanyu.a11.com. (
                        2023101101      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.a11.com.
@       IN      A       192.174.4.3     ; IP Abimanyu
www     IN      CNAME   abimanyu.a11.com.
parikesit IN    A       192.174.4.3     ; IP Abimanyu
```

Setelah itu restart bind menggunakan ```service bind9 restart```

Lalu coba di test dengan cara ping di Nakula atau Sadewa menggunakan
```shell
ping parikesit.abimanyu.a11.com
```

**Hasil**

![image](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/e8c021aa-2b56-4cfc-8138-3b001b2f065c)

## Soal 5
> Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

Untuk melakukan reverse domain. Kita perlu untuk mengetahui IP dari Abimanyu. Karena IP Abimanyu kami adalah ```192.174.4.3```, perlu mengubahnya menjadi ```3.4.174.192```

Edit file /etc/bind/named.conf.local dengan menambahkan :
```shell
zone "2.174.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.174.192.in-addr.arpa";
};
```

Kemudian copy file db.local
```shell
cp /etc/bind/db.local /etc/bind/jarkom/2.174.192.in-addr.arpa
```

Lalu masuk ke file 2.174.192.in-addr.arpa, kemudian ubah isinya seperti di bawah ini :
```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.a11com. root.abimanyu.a11.com. (
                       2023101101       ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
4.174.192.in-addr.arpa. IN      NS      abimanyu.a11.com.
3                       IN      PTR     abimanyu.a11.com.       ;Byte ke-4
```

Kemudian pada clien (Nakula atau Sadewa) dilakukan update dan install menggunakan :
```
apt-get update
apt-get install dnsutils
```
Sebelum mengakses, jangan lupa untuk mengembalikan ```nameserver``` ke DNS Master 
Kemudian akses menggunakan :
```
host -t PTR 192.174.4.3
```

**Hasil**

![image](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/e182fa43-210e-4176-bdf7-40c08bb1ee1f)

## Soal 6
> Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama

Pada DNS Master diperlukan setup also-notify dan allow-transfer agar memberikan izin kepada IP yang dituju.
Sehingga kita menambahkan 

```
notify yes;
        also-notify { 192.174.1.2; };
        allow-transfer { 192.174.1.2; };
```

Hasilnya akan menjadi seperti di bawah ini :
```shell
echo 'zone "arjuna.a11.com" {
        type master;
        notify yes;
        also-notify { 192.174.1.2; };
        allow-transfer { 192.174.1.2; };
        file "/etc/bind/jarkom/arjuna.a11.com";
};

zone "abimanyu.a11.com" {
        type master;
        //notify yes;
        //also-notify { 192.174.1.2; };
        allow-transfer { 192.174.1.2; };
        file "/etc/bind/jarkom/abimanyu.a11.com";
};

zone "4.174.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/4.174.192.in-addr.arpa";
};
```
Kemudian restart menggunakan ```service bind9 restart```. Setelah itu bind9 akan dimatikan menggunakan
```service bind9 stop```

Kemudian, kita masuk ke Werkudara. Pertama update package list dan install bind9. Setelah berhasil di update dan install, kita masuk ke file /etc/bind/named.conf.local pada Werkudara. 
Lalu edit file sesuai dengan di bawah ini :

```shell
'zone "arjuna.a11.com" {
        type slave;
        masters { 192.174.2.2; };
        file "/var/lib/bind/arjuna.a11.com";
};

zone "abimanyu.a11.com" {
        type slave;
        masters { 192.174.2.2; };
        file "/var/lib/bind/abimanyu.a11.com";
};
```
Lakukan restart bind9. Lalu pada client pastikan nameserver menuju ke IP Yudhistira dan Werkudar.
Kemudian test menggunakan
```shell
ping abimanyu.a11.com 
ping www.abimanyu.a11.com 
```