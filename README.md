# Jarkom-Modul-2-A11-2023
**Praktikum Jaringan Komputer Modul 2 Tahun 2023**

### Penulis
| Nama | NRP |Github |
|---------------------------|------------|--------|
|Muhammad Zien Zidan | 5025211122 | https://github.com/zienzidan |
|Glenaya | 5025211202 | https://github.com/nyawnayaw05 |

## Soal 1
> Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut.

Dalam soal ini, kami mendapatkan plottingan soal Topologi No. 3


![Screenshot 2023-10-17 172449](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/c6d52c82-df9f-4024-9ef8-3a4694551ac5)


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

![Screenshot 2023-10-17 174352](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/39ada706-025e-4fd1-8c5b-fb122d7e31ef)

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

![Screenshot 2023-10-17 183333](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/60912259-0523-4b1c-ae2e-0e6457a46160)


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
![Screenshot 2023-10-17 183952](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/4c4e100f-7f46-4ae3-8287-5b509f0676ee)



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
![Screenshot 2023-10-17 184913](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/996c35fd-2e61-4679-ba7e-50da7f67d0aa)



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

**Hasil**

![Screenshot 2023-10-17 191748](https://github.com/zienzidan/Jarkom-Modul-2-A11-2023/assets/114491445/998d4655-97cc-420e-bded-5ce8b7d31580)

## Soal 7
>Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

Lakukan edit file yang terdapat di etc/bind/jarkom/abimanyu.a11.com pada DNS Mater (Yudhidtira), seperti di bawah ini :

```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.a11.com.       root.abimanyu.a11.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800  )      ; Negative Cache TTL
;
@               IN      NS      abimanyu.a11.com.
@               IN      A       192.174.4.3     ; IP Abimanyu
www             IN      CNAME   abimanyu.a11.com.
parikesit       IN      A       192.174.4.3
ns1             IN      A       192.174.1.2     ; IP Werkudara
baratayuda      IN      NS      ns1
@               IN      AAAA    ::1
```

Edit juga pada /etc/bind/named.conf.options dengan isi di bawah ini :
```shell
//dnssec-validation auto;
allow-query{any;};

auth-nxdomain no;    # conform to RFC1035
listen-on-v6 { any; };
```

Kemudian kita akan pindah ke DNS Master Werkudara. Lalu edit file /etc/bind/named.conf.options
dengan isi di bawah ini :

```shell
//dnssec-validation auto;
allow-query{any;};

auth-nxdomain no;    # conform to RFC1035
listen-on-v6 { any; };
```

Kemudian pada file /etc/bind.named.conf.local edit file sehingga menjadi seperti ini :
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

zone "baratayuda.abimanyu.a11.com" {
        type master;
        file "/etc/bind/baratayuda/baratayuda.abimanyu.a11.com";
};
```

Kemudian buat direktori bernama baratayuda dengan cara ```mkdir /etc/bind/baratayuda```. Setelah itu copy db.local ke direktori tersebut ```cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.a11.com```.

Lalu edit pada file /etc/bind/baratayuda/baratayuda.abimanyu.a11.co sehingga seperti di bawah ini :

```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.a11.com. root.baratayuda.abimanyu.a11.com. (
                        2               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.a11.com.
@               IN      A       192.174.4.3   ; IP Abimanyu
www             IN      CNAME   baratayuda.abimanyu.a11.com.
```

Setelah itu lakukan restart pada bind9 dengan ```service bind9 restart```.

Terakhir lakukan testing pada client (Nakula atau Sadewa)
```
ping baratayuda.abimanyu.a11.com
ping www.baratayuda.abimanyu.a11.com
```

**Hasil**
![first](image/7.png)

## Soal 8
> Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

Pada soal ini kita hanya akan mengedit pada file /etc/bind/baratayuda/baratayuda.abimanyu.a11.com sehingga menjadi seperti di bawah ini :

```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.a11.com. root.baratayuda.abimanyu.a11.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.a11.com.
@       IN      A       192.174.4.3     ; IP Abimanyu
www     IN      CNAME   baratayuda.abimanyu.a11.com.
rjp     IN      A       192.174.4.3
www.rjp IN      CNAME   rjp
```

Kemudian restart bind9 dengan ```service bind9 restart```

Lalu lakukan testing pada client (Nakula atau Sadewa)
```
ping rjp.abimanyu.a11.com
ping www.rjp.abimanyu.a11.com
```

**Hasil**

![first](image/8.png)

### Soal 9
>Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### Soal 10
>Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh:    - Prabakusuma:8001 - Abimanyu:8002 - Wisanggeni:8003

#### Arjuna
```
apt-get update 

apt-get install nginx -y

echo 'upstream myweb  {
        server 192.174.4.2:8001;
        server 192.174.4.3:8002;
        server 192.174.4.4:8003;

}

server {
        listen 80;
        server_name arjuna;

        location / {
                proxy_pass http://myweb;
        }
}' > /etc/nginx/sites-available/lb-arjuna

ln -s /etc/nginx/sites-available/lb-arjuna /etc/nginx/sites-enabled

rm -rf /etc/nginx/sites-enabled/default 

service nginx restart
```

#### Prabukusuma
```
apt-get update

apt-get install wget unzip nginx php php-fpm -y

wget -O file.zip “https://drive.google.com/uc?export=download&id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX”

unzip file.zip

mv arjuna.yyy.com /var/www/arjuna.a11

rm file.zip

nano /etc/nginx/sites-available/arjuna.a11

echo 'server {

            listen 8001;

            root /var/www/arjuna.a11;

            index index.php index.html index.htm;
            server_name arjuna;

            location / {
                        try_files $uri $uri/ /index.php?$query_string;
            }

            # pass PHP scripts to FastCGI server
            location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
            }

        location ~ /\.ht {
                        deny all;
	}

	error_log /var/log/nginx/arjuna_error.log;
	access_log /var/log/nginx/arjuna_access.log;
}' > /etc/nginx/sites-available/arjuna.a11

ln -s /etc/nginx/sites-available/arjuna.a11 /etc/nginx/sites-enabled

rm -rf /etc/nginx/sites-enabled/default 

service nginx restart 

service php7.0-fpm start

```
#### Abimanyu
```
apt-get update

apt-get install wget unzip nginx php php-fpm -y

wget -O file.zip “https://drive.google.com/uc?export=download&id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX”

unzip file.zip

mv arjuna.yyy.com /var/www/arjuna.a11

rm file.zip

nano /etc/nginx/sites-available/arjuna.a11

echo 'server {

            listen 8002;

            root /var/www/arjuna.a11;

            index index.php index.html index.htm;
            server_name arjuna;

            location / {
                        try_files $uri $uri/ /index.php?$query_string;
            }

            # pass PHP scripts to FastCGI server
            location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
            }

        location ~ /\.ht {
                        deny all;
	}

	error_log /var/log/nginx/arjuna_error.log;
	access_log /var/log/nginx/arjuna_access.log;
}' > /etc/nginx/sites-available/arjuna.a11

ln -s /etc/nginx/sites-available/arjuna.a11 /etc/nginx/sites-enabled

rm -rf /etc/nginx/sites-enabled/default 

service nginx restart 

service php7.0-fpm start

```
#### Wisanggeni
```
apt-get update

apt-get install wget unzip nginx php php-fpm -y

wget -O file.zip “https://drive.google.com/uc?export=download&id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX”

unzip file.zip

mv arjuna.yyy.com /var/www/arjuna.a11

rm file.zip

nano /etc/nginx/sites-available/arjuna.a11

echo 'server {

            listen 8001;

            root /var/www/arjuna.a11;

            index index.php index.html index.htm;
            server_name arjuna;

            location / {
                        try_files $uri $uri/ /index.php?$query_string;
            }

            # pass PHP scripts to FastCGI server
            location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
            }

        location ~ /\.ht {
                        deny all;
	}

	error_log /var/log/nginx/arjuna_error.log;
	access_log /var/log/nginx/arjuna_access.log;
}' > /etc/nginx/sites-available/arjuna.a11

ln -s /etc/nginx/sites-available/arjuna.a11 /etc/nginx/sites-enabled

rm -rf /etc/nginx/sites-enabled/default 

service nginx restart 

service php7.0-fpm start

```

#### Nakula
```
apt-get install lynx

lynx arjuna.a11.com
```
> Hasil

![first](image/10%20(3).png)

![first](image/10%20(1).png)

![first](image/10%20(2).png)

### Soal 11
>Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

#### Abimanyu
```
wget -O file.zip "https://drive.google.com/uc?export=download&id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc"

unzip file.zip

mv abimanyu.yyy.com /var/www/abimanyu.a11

rm file.zip

apt-get update

apt-get install apache2 php libapache2-mod-php7.0 -y

service apache2 start

echo '<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.a11

        ServerName abimanyu.a11.com
        ServerAlias www.abimanyu.a11.comRedirect permanent /index.php/home http://www.abimanyu.a11.com/home/
        Redirect permanent /192.174.4.3/ https://www.abimanyu.a11.com
        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.a11.com.conf

a2ensite abimanyu.a11.com 

service apache2 restart
```
Tambahkan pada file /etc/apache2/sites-available/abimanyu.a11.com.conf
```
<Directory /var/www/abimanyu.a11.com/home>
        Options +Indexes
</Directory>
```
> Lakukan Testing

```
lynx www.abimanyu.a11.com
```
> Hasil

![first](image/11.png)

### Soal 12
>Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

ubah file  /etc/apache2/sites-available/abimanyu.a11.com.conf dan tambahkan
#### Abimanyu
```
Redirect permanent /index.php/home http://www.abimanyu.a11.com/home/
```
secara utuh 
```
echo '<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.a11

        ServerName abimanyu.a11.com
        ServerAlias www.abimanyu.a11.com

        <Directory /var/www/abimanyu.a11.com/home>
                Options +Indexes
        </Directory>

        Redirect permanent /index.php/home http://www.abimanyu.a11.com/home/

         # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.a11.com.conf

service apache2 restart
```
>Lakukan Testing
```
lynx abimanyu.a11.com/home
```
![first](image/11.png)

### Soal 13
>Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

#### Abimanyu
```
wget -O file.zip “https://drive.google.com/uc?export=download&id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS”

unzip file.zip

mv parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.a11

rm file.zip
```
Edit file /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf 
```
echo '<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.a11
        ServerName parikesit.abimanyu.a11.com
        ServerAlias www.parikesit.abimanyu.a11.com
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</Virtualhost>' > /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf 

a2ensite parikesit.abimanyu.a11.com
```

>Lakukan Testing
```
lynx parikesit.abimanyu.a11.com
```
>Hasil

![first](image/13.png)

### Soal 14
>Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

ubah file /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf 
#### Abimanyu
```
echo '<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.a11
        ServerName parikesit.abimanyu.a11.com
        ServerAlias www.parikesit.abimanyu.a11.com
        <Directory /var/www/parikesit.abimanyu.a11/public>
                Options +Indexes
        </Directory>
        <Directory /var/www/parikesit.abimanyu.a11/secret>
                Options -Indexes +FollowSymLinks
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</Virtualhost>' > /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf 
```
Buat file /var/www/parikesit.abimanyu.a11/secret/.htaccess
```
Require all denied
``` 
>Lakukan Testing
```
lynx parikesit.abimanyu.a11.com/public
lynx parikesit.abimanyu.a11.com/secret
```
>Hasil

![first](image/14%20(2).png)

![first](image/14%20(1).png)

### Soal 15
>Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden

Edit file /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf
tambahkan 
```
ErrorDocument 403 /error/403.php
ErrorDocument 404 /error/404.php 
```
#### Abimanyu
```
echo '<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.a11
        ServerName parikesit.abimanyu.a11.com
        ServerAlias www.parikesit.abimanyu.a11.com
        <Directory /var/www/parikesit.abimanyu.a11/public>
                Options +Indexes
        </Directory>
        <Directory /var/www/parikesit.abimanyu.a11/secret>
                Options -Indexes +FollowSymLinks
        </Directory>
         ErrorDocument 403 /error/403.html
         ErrorDocument 404 /error/404.html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</Virtualhost>' > /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf
```
>Lakukan Testing
```
lynx parikesit.abimanyu.a11.com/secret
lynx parikesit.abimanyu.a11.com/abcde 
```
>Hasil

![first](image/15%20(1).png)

![first](image/15%20(2).png)

![first](image/15%20(3).png)

![first](image/15%20(4).png)

### Soal 16
>Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi www.parikesit.abimanyu.yyy.com/js 

Ubah file /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf
tambahkan 
```
Alias /js /var/www/parikesit.abimanyu.a11/public/js 
```
#### Abimanyu
```
echo '<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.a11
        ServerName parikesit.abimanyu.a11.com
        ServerAlias www.parikesit.abimanyu.a11.com
        <Directory /var/www/parikesit.abimanyu.a11/public>
                Options +Indexes
        </Directory>
        <Directory /var/www/parikesit.abimanyu.a11/secret>
                Options -Indexes +FollowSymLinks
        </Directory>
        Alias /js /var/www/parikesit.abimanyu.a11/public/js
        ErrorDocument 403 /error/403.html
        ErrorDocument 404 /error/404.html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</Virtualhost>' > /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf
```

>Lakukan Testing
```
lynx parikesit.abimanyu.a11.com/js
```

>Hasil

![first](image/16.png)

### Soal 17
>Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

#### Abimanyu
```
wget -O file.zip “https://drive.google.com/uc?export=download&id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6”

unzip file.zip

mv rjp.baratayuda.abimanyu.yyy.com /var/www/rjp.baratayuda.abimanyu.a11

rm file.zip
```
Buat file /etc/apache2/sites-available/rjp.baratayuda.abimanyu.a11.com.conf 
```
echo '<VirtualHost *:14000>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.a11
    ServerName rjp.baratayuda.abimanyu.a11.com
    ServerAlias www.rjp.baratayuda.abimanyu.a11.com

    <Directory /var/www/rjp.baratayuda.abimanyu.a11>
        Options +Indexes
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:14400>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.a11
    ServerName rjp.baratayuda.abimanyu.a11.com
    ServerAlias www.rjp.baratayuda.abimanyu.a11.com

    <Directory /var/www/rjp.baratayuda.abimanyu.a11>
        Options +Indexes
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' >  /etc/apache2/sites-available/rjp.baratayuda.abimanyu.a11.com.conf
```
Edit file /etc/apache2/ports.conf dan tambahkan Listen 14000 dan Listen 14400
```
echo 'Listen 80
Listen 14000
Listen 14400

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>' > /etc/apache2/ports.conf
```
lakukan perintah
```
a2ensite rjp.baratayuda.abimanyu.a11.com

service apache2 restart
```
>Lakukan Testing
```
lynx rjp.baratayuda.abimanyu.a11.com:14000

lynx rjp.baratayuda.abimanyu.a11.com:14400
```
> Hasil

![first](image/17%20(2).png)

![first](image/17%20(1).png)

### Soal 18
>Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy

Ubah file /etc/apache2/sites-available/rjp.baratayuda.abimanyu.a11.com.conf 

#### Abimanyu
```
echo '<VirtualHost *:14000>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.a11
    ServerName rjp.baratayuda.abimanyu.a11.com
    ServerAlias www.rjp.baratayuda.abimanyu.a11.com

    <Directory /var/www/rjp.baratayuda.abimanyu.a11>
        Options +Indexes
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    <Location />
            AuthType Basic
            AuthName "Restricted Area"
            AuthUserFile /etc/apache2/.htpasswd
            Require valid-user
    </Location>
</VirtualHost>

<VirtualHost *:14400>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/rjp.baratayuda.abimanyu.a11
    ServerName rjp.baratayuda.abimanyu.a11.com
    ServerAlias www.rjp.baratayuda.abimanyu.a11.com

    <Directory /var/www/rjp.baratayuda.abimanyu.a11>
        Options +Indexes
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    <Location />
            AuthType Basic
            AuthName "Restricted Area"
            AuthUserFile /etc/apache2/.htpasswd
            Require valid-user
    </Location>
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.a11.com.conf
```
Lakukan perintah 
```
htpasswd -c -B -b /etc/apache2/.htpasswd Wayang baratayudaa11

a2enmod authn_core

a2enmod auth_basic

a2enmod authn_file

service apache2 restart
```

>Lakukan Testing
```
lynx rjp.baratayuda.abimanyu.a11.com:14000

lynx rjp.baratayuda.abimanyu.a11.com:14400
```
>Hasil

![first](image/18%20(1).png)

![first](image/18%20(2).png)

![first](image/18%20(3).png)

![first](image/18%20(4).png)

![first](image/18%20(2).png)

![first](image/18%20(5).png)

### Soal 19
>Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

Edit file /etc/apache2/sites-available/abimanyu.a11.com.conf
tambahkan
```
Redirect permanent /192.174.4.3/ https://www.abimanyu.a11.com
``` 
#### Abimanyu
```
echo '<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.a11

        ServerName abimanyu.a11.com
        ServerAlias www.abimanyu.a11.com

        <Directory /var/www/abimanyu.a11.com/home>
                Options +Indexes
        </Directory>

        Redirect permanent /index.php/home http://www.abimanyu.a11.com/home/
        Redirect permanent /192.174.4.3/ https://www.abimanyu.a11.com
        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular'
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.a11.com.conf
```
Lakukan Perintah
```
a2dissite 000-default.conf 

service apache2 restart
```
>Lakukan Testing
```
lynx 192.174.4.3
```
>Hasil

![first](image/19.png)

### Soal 20
>Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

#### Abimanyu
lakukan perintah
```
a2enmod rewrite
```
Buat file /var/www/parikesit.abimanyu.a11/.htaccess 
```
echo 'RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)(abimanyu)(.*\.(png|jpg))
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule abimanyu http://parikesit.abimanyu.a11.com/public/images/abimanyu.png$1 [L,R=301]' > /var/www/parikesit.abimanyu.a11/.htaccess 
```
Ubah konfigurasi dan gunakan AllowOverride All untuk mengkonfigurasi nya dengan .htaccess sebelumnya
```
echo '<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.a11
        ServerName parikesit.abimanyu.a11.com
        ServerAlias www.parikesit.abimanyu.a11.com
        <Directory /var/www/parikesit.abimanyu.a11/public>
                Options +Indexes
        </Directory>
        <Directory /var/www/parikesit.abimanyu.a11/secret>
                Options -Indexes +FollowSymLinks
        </Directory>
        <Directory /var/www/parikesit.abimanyu.a11>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
        Alias /js /var/www/parikesit.abimanyu.a11/public/js
        ErrorDocument 403 /error/403.html
        ErrorDocument 404 /error/404.html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</Virtualhost>' > /etc/apache2/sites-available/parikesit.abimanyu.a11.com.conf

service apache2 restart
```
>Lakukan Testing
```
lynx parikesit.abimanyu.a11.com/public/images/not-abimanyu.png
```
>Hasil

![first](image/20%20(1).png)

![first](image/20%20(3).png)

![first](image/20%20(2).png)