# Jarkom-Modul-3-A11-2023

**Praktikum Jaringan Komputer Modul 3 Tahun 2023**

## Penulis

| Nama                | NRP        | Github                         |
| ------------------- | ---------- | ------------------------------ |
| Muhammad Zien Zidan | 5025211122 | https://github.com/zienzidan   |
| Glenaya             | 5025211202 | https://github.com/nyawnayaw05 |

### GNS Project
**Link Project :** https://its.id/m/Project-GNS

### Topologi

![Alt text](Image/image.png)

### Configurasi

- **AURA (DHCP Relay)**

```shell
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.174.1.0
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.174.2.0
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.174.3.0
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.174.4.0
	netmask 255.255.255.0
```

- **HIMMEL (DHCP Server)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.1.1
	netmask 255.255.255.0
	gateway 192.174.1.0
```

- **HEITER (DNS Server)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.1.2
	netmask 255.255.255.0
	gateway 192.174.1.0
```

- **DENKEN (Database Server)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.2.1
	netmask 255.255.255.0
	gateway 192.174.2.0
```

- **EISEN (Load Balancer)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.2.2
	netmask 255.255.255.0
	gateway 192.174.2.0
```

- **FRIEREN (Laravel Worker)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.4.3
	netmask 255.255.255.0
	gateway 192.174.4.0
```

- **FLAMME (Laravel Worker)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.4.2
	netmask 255.255.255.0
	gateway 192.174.4.0
```

- **FERN (Laravel Worker)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.4.1
	netmask 255.255.255.0
	gateway 192.174.4.0
```

- **LAWINE (PHP Worker)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.3.3
	netmask 255.255.255.0
	gateway 192.174.3.0
```

- **LINIE (PHP Worker)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.3.2
	netmask 255.255.255.0
	gateway 192.174.3.0
```

- **LUGNER (PHP Worker)**

```shell
auto eth0
iface eth0 inet static
	address 192.174.3.1
	netmask 255.255.255.0
	gateway 192.174.3.0
```

- **REVOLTE, RITCHTER, SEIN, STARK (Client)**

```shell
auto eth0
iface eth0 inet dhcp
```

### Soal 1

> Lakukan konfigurasi sesuai dengan peta yang sudah dieberikan.

Pada Heiter lakukan seperti di bawah ini terlebih dahulu

```shell
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```

Kemudian pada `/etc/bind/named.conf.options` tambahkan :

```shell
options {
    directory "/var/cache/bind";
forwarders {
    192.168.122.1;
    };
 //dnssec-validation auto;
allow-query{any;};
auth-nxdomain no;
listen-on-v6 { any; }
```

Lalu lakukan `service bind9 restart`

Selanjutnya pada file `etc/bind/named.conf.local` tambahkan :

```shell
zone "granz.channel.a11.com" {
	type master;
	file "/etc/bind/jarkom/granz.channel.a11";
};
zone "riegel.canyon.a11.com" {
	type master;
	file "/etc/bind/jarkom/riegel.canyon.a11";
};
```

Kemudian buat file baru menggunakan `mkdir /etc/bind/jarkom` lalu copy isi `db.local` dengan cara

```shell
cp /etc/bind/db.local /etc/bind/jarkom/riegel.canyon.a11.com
cp /etc/bind/db.local /etc/bind/jarkom/granz.channel.a11.com
```

Lalu pada `/etc/bind/jarkom/riegel.canyon.a11` isi dengan :

```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     riegel.canyon.a11.com. 	root.riegel.canyon.a11.com. (
                     		2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      	NS      	riegel.canyon.a11.com.
@       	IN      	A		192.174.4.1 // IP FREN
www	IN	CNAME	riegel.canyon.a11.com.
```

Selanjutnya pada `/etc/bind/jarkom/granz.channel.a11.com` isi dengan :

```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA    granz.channel.a11.com. 	root.granz.channel.a11.com. (
                     		2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      	NS      	granz.channel.a11.com.
@       	IN      	A		192.174.3.1 //IP Lugner
www	IN	CNAME	granz.channel.a11.com.
```

Kemudian lakukan `service bind9 start`

**Hasil**
![Alt text](Image/image-1.png)
![Alt text](Image/image-2.png)

### Soal 2

> Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80

Pada Himmel (DHCP Server) lakukan hal ini terlebih dahulu :

```shell
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server
dhcpd --version
```

Kemudian pada `/etc/default/isc-dhcp-server` tambahkan

```shell
INTERFACESv4="eth0"
```

Lalu pada ` etc/dhcp/dhcpd.conf` tambahkan :

```shell
subnet 192.174.1.0 netmask 255.255.255.0{
}

subnet 192.174.2.0 netmask 255.255.255.0{
}

subnet 192.174.3.0 netmask 255.255.255.0{
        range 192.174.3.16 192.174.3.32;
        range 192.174.3.64 192.174.3.80;
        option routers 192.174.3.0;
}
```

Kemudian restart DHCP Server menggunakan `service isc-dhcp-server restart`

### Soal 3

> Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168

Kita melanjutkan dari soal nomor 2 yaitu menambahkan konfigurasi baru untuk Switch4 sehingga script akan menjadi seperti di bawah ini :

```shell
subnet 192.174.1.0 netmask 255.255.255.0{
}

subnet 192.174.2.0 netmask 255.255.255.0{
}

subnet 192.174.3.0 netmask 255.255.255.0{
        range 192.174.3.16 192.174.3.32;
        range 192.174.3.64 192.174.3.80;
        option routers 192.174.3.0;
        option broadcast-address 192.174.3.255;
}

subnet 192.174.4.0 netmask 255.255.255.0{
        range 192.174.4.12 192.174.4.20;
        range 192.174.4.160 192.174.4.168;
        option routers 192.174.4.0;
}
```

### Soal 4

> Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut

Menambahkan konfigurasi `option broadcast-address` dan `option domain-name-server` pada Himmel yang terdapat di nomor 2 dan 3. Sehingga menjadi seperti di bawah ini :

```shell
subnet 192.174.1.0 netmask 255.255.255.0{
}

subnet 192.174.2.0 netmask 255.255.255.0{
}

subnet 192.174.3.0 netmask 255.255.255.0{
        range 192.174.3.16 192.174.3.32;
        range 192.174.3.64 192.174.3.80;
        option routers 192.174.3.0;
        option broadcast-address 192.174.3.255;
        option domain-name-servers 192.174.1.2;
}

subnet 192.174.4.0 netmask 255.255.255.0{
        range 192.174.4.12 192.174.4.20;
        range 192.174.4.160 192.174.4.168;
        option routers 192.174.4.0;
        option broadcast-address 192.174.4.255;
        option domain-name-servers 192.174.1.2;;
}
```

Jangan lupa lakukan restart dengan cara `service isc-dhcp-server restart`

Kemudian pada Aura (DHCP Relay) lakukan ini terlebih dahulu

```shell
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.174.0.0/16
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
```

Lalu pada `/etc/default/isc-dhcp-relay` tambahkan :

```shell
SERVERS=”192.174.1.1"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=
```

Selanjutnya pada `/etc/sysctl.conf` tambahkan

```shell
net.ipv4.ip_forward=1
```

Kemudian restart dengan menggunakan `service isc-dhcp-relay restart`

**Hasil**

Merupakan hasil percobaan di client `Stark`
![Alt text](Image/image-3.png)

## Soal 5
> Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit

Menambahkan fungsi ```default-lease-time``` dan ```max-lease-time``` pada Himmel (DHCP Server). Pada Switch3 dapat meminjam IP DHCP Server (Himmel) selama 3 menit yaitu ```180 s``` dan Switch4 dapat meminjam IP DHCP (Himmel) selamat 12 menit yaitu ```720 s```. Sedangkan peminjaman alamat IP adalah 96 menit atau sama dengan ```5760 s```. Sehingga pada ```etc/dhcp/dhcpd.conf``` berubah menjadi seperti di bawah ini :

```shell
subnet 192.174.1.0 netmask 255.255.255.0{
}

subnet 192.174.2.0 netmask 255.255.255.0{
}

subnet 192.174.3.0 netmask 255.255.255.0{
        range 192.174.3.16 192.174.3.32;
        range 192.174.3.64 192.174.3.80;
        option routers 192.174.3.0;
        option broadcast-address 192.174.3.255;
        option domain-name-servers 192.174.1.2;
        default-lease-time 180;
        max-lease-time 5760;
}

subnet 192.174.4.0 netmask 255.255.255.0{
        range 192.174.4.12 192.174.4.20;
        range 192.174.4.160 192.174.4.168;
        option routers 192.174.4.0;
        option broadcast-address 192.174.4.255;
        option domain-name-servers 192.174.1.2;
        default-lease-time 720;
        max-lease-time 5760;
}
```
Jangan lupa lakukan restart dengan cara `service isc-dhcp-server restart`

**Hasil**

![Alt text](Image/no.5%20switch4.png)
![Alt text](Image/no.5%20switch%203.png)

### Soal 6
> Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3.

Lakukan set up terlebih dahulu pada Lugner, Linie, Lawine (PHP Worker) dengan cara :
```shell
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install wget unzip php7.3 php7.3-fpm nginx -y
```

Setelah itu kita akan melakukan seperti yang dibawah ini. Kita menggunakan ```wget``` untuk download dan unzip.

```shell
wget -O file.zip "https://drive.google.com/uc?export=download&id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTP rU1"
unzip file.zip 
mv modul-3 /var/www/granz.channel.a11 
rm file.zip
```

Lalu pada ```/etc/nginx/sites-available/granz.channel.a11``` tambahkan : 
```shell
server {
listen 80;

root /var/www/granz.channel.a11;

index index.php index.html index.htm;
server_name granz.channel.a11.com;
location / {
	try_files $uri $uri/ /index.php?$query_string;
}

# pass PHP scripts to FastCGI server
location ~ \.php$ {
	include snippets/fastcgi-php.conf;
	fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
}

location ~ /\.ht {
	deny all;
}
error_log /var/log/nginx/jarkom_error.log;
access_log /var/log/nginx/jarkom_access.log;
}

ln -s /etc/nginx/sites-available/granz.channel.a11 /etc/nginx/sites-enabled
```

Kemudian lakukan ```rm -rf /etc/nginx/sites-enabled/default```. Selanjutnya lakukan restart menggunakan ```service nginx restart``` dan ```service php7.3-fpm start```

### Soal 7
> Kepala suku dari Bredt Region memberikan resource server sebagai berikut:
a. Lawine, 4GB, 2vCPU, dan 80 GB SSD.
b. Linie, 2GB, 2vCPU, dan 50 GB SSD.
c. Lugner 1GB, 1vCPU, dan 25 GB SSD.
aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second.

Pada Eisen (Load Balancer) lakukan set up terlebih dahulu dengan cara : 
```shell
echo 'nameserver 192.168.122.1' > /etc/resolv.conf 
apt-get update 
apt-get install nginx apache2-utils -y
```
Lalu pada Heiter (DNS Server) kembalikan IP ke Eisen (Load Balancer) sehingga IP menjadi ```192.174.2.2```

Selanjutnya pada ```/etc/nginx/sites-available/granz.channel.a11``` yang terdapat pada Eisen tambahkan: 
```shell
upstream myweb  {
server 192.174.3.1 weight=1; #IP Lugner
server 192.174.3.2 weight=4; #IP Lignie	
server 192.174.3.3 weight=8; #IP Lawine
 }
server {
	listen 80;
	server_name granz.channel.a11.com ;
        } 

location / {
        proxy_pass http://worker;
        }
}

``` 
Kemudian lakukan ```rm -rf /etc/nginx/sites-enabled/default``` lalu ```ln -s /etc/nginx/sites-available/riegel.canyon.a11 /etc/nginx/sites-enabled```. Jangan lupa lakukan restart dengan ```service nginx restart```
Selanjutnya pada Revolte (Client) lakukan perintah ini:
```shell 
ab -n 1000 -c 100 http://www.granz.channel.a11.com/ 
```

**Hasil**

![Alt text](Image/no.7(1).png)
![Alt text](Image/no.7(2).png)
![Alt text](Image/no.7(3).png)

### Soal 8
> Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
a. Nama Algoritma Load Balancer
b. Report hasil testing pada Apache Benchmark
c. Grafik request per second untuk masing masing algoritma. 
d. Analisis

Hal yang dilakukan sama.Laporan grimoire sudah ada di atas bagian laporan resmi ini. Untuk dapat melihat hasil lakukan pada Revolte (Client)
```shell
ab -n 200 -c 10 http://www.granz.channel.a11.com/ 
```
**Hasil**
**Round Robin**
![Alt text](Image/no.8%20round%20robin.png)

**Least-Connection**
![Alt text](Image/no.8%20least-connection.png)

**IP-Hash**
![Alt text](Image/no.8%20IP%20Hash.png)

**Generic Hash**
![Alt text](Image/no.8%20generic.png)

**Grafik**

![Alt text](Image/no.8%20grafik.png)

### Soal 9
> Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire.

Kita melakukan testing lagi hampir sama dengan nomor 8 yang membedakan adalah testing menggunakan 1 worker, 2 worker dan 3 worker

**Hasil**
Pada 3 Worker

![Alt text](Image/no.10%203%20worker.png)

Pada 2 Worker

![Alt text](Image/no.10%202%20worker.png)

Pada 1 Worker

![Alt text](Image/no.10%201%20worker.png)

Grafik
![Alt text](Image/no.10%20grafik.png)

### Soal 10
> Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/

Kembali ke dalam Eisen (Load Balance). Lalu lakukan:
```shell
rm -rf /etc/nginx/rahasiakita 
mkdir /etc/nginx/rahasiakita 
htpasswd -c -b /etc/nginx/rahasiakita/.htpasswd netics ajka11 
```

Kemudian pada file ```/etc/nginx/sites-available/granz.channel.a11``` ditambahkan:
```shell
auth_basic "Restricted Area"; 
auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
```

Sehingga akan menjadi
```shell
server {
	listen 80;
	server_name granz.channel.a11.com ;

	location / {
	auth_basic "Restricted Area"; 
        auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
	proxy_pass http://myweb;
	}
}

```

### Soal 11

> Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id.

Tambahkan proxy its didalam file `/etc/nginx/sites-available/granz.channel.a11` pada nginx yang ada di `load balancer`

#### Script

```
location /its {
proxy_pass https://www.its.ac.id;
}
```

#### Full Script

```
server {
	listen 80;
	server_name granz.channel.a11.com ;

        location / {
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
        proxy_pass http://myweb;
        }

        location /its {
        proxy_pass https://www.its.ac.id;
        }
}
```

> Lakukan Testing

```
lynx www.granz.channel.a11.com/its
```

**Hasil**

![first](Image/11.png)

### Soal 12

> Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168.

ubah file `/etc/nginx/sites-available/granz.channel.a11` dan tambahkan allow dari IP yang di inginkan oleh soal

#### Script

```
allow 192.174.3.69;
allow 192.174.3.70;
allow 192.174.4.167;
allow 192.174.4.168;
deny all;
```

#### Full Script

```
server {
	listen 80;
	server_name granz.channel.a11.com ;

        location / {
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
        proxy_pass http://myweb;
        }

        location /its {
        proxy_pass https://www.its.ac.id;
        }

        allow 192.174.3.69;
        allow 192.174.3.70;
        allow 192.174.4.167;
        allow 192.174.4.168;
        deny all;
}

rm -rf /etc/nginx/sites-enabled/default

ln -s /etc/nginx/sites-available/granz.channel.a11 /etc/nginx/sites-enabled

service nginx restart
```

> Lakukan Testing

```
lynx granz.channel.a11.com
```

**Hasil**

![first](Image/12.%201.png)

![first](Image/12.2.png)

![first](Image/12.%203.png)

### Soal 13

> Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern.

Lakukan setup di `database server` yaitu pada node `Denken`

#### Script

```
echo ' [client-server]
[mysqld]
skip-networking=0
skip-bind-address
!includedir /etc/mysql/conf.d/
!includeidr /etc/mysql/mariadb.conf.d/
' > /etc/mysql/my.cnf
```

Lakukan perintah seperti berikut didalam mariadb

```
mysql -u root -e "CREATE DATABASE dbkelompoka11";
mysql -u root -e "CREATE USER 'kelompoka11'@'%' IDENTIFIED BY 'passworda11'";
mysql -u root -e "GRANT ALL PRIVILEGES ON *.* TO 'kelompoka11'@'%'";
mysql -u root -e "FLUSH PRIVILEGES";

service mysql restart
```

> Lakukan Testing

untuk melihat apakah database bisa terkoneksi dengan `laravel worker` .

```
mariadb --host=192.174.2.1 --port=3306 --user=kelompoka11 --password=passworda11

show databases;
```

**Hasil**

![first](Image/13.png)

### Soal 14

> Frieren, Flamme, dan Fern memiliki Granz Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer

Lakukan set up pada laravel worker

#### Script

```
wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer
```

Lakukan install `git` dan lakukan cloning

```
apt-get install git -y
rm -rf /var/www/laravel-praktikum-jarkom
cd /var/www
git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git
cd laravel-praktikum-jarkom
composer update
touch .env
```

setelah itu, lakukan configurasi pada seluruh worker

```
echo ' APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=192.174.2.1
DB_PORT=3306
DB_DATABASE=dbkelompoka11
DB_USERNAME=kelompoka11
DB_PASSWORD=passworda11

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
' > .env
php artisan migrate:fresh --seeder=AiringsTableSeeder
php artisan key:generate
php artisan jwt:secret

rm -rf /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/praktikum-jarkom /etc/nginx/sites-enabled/
chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage
```

Setelah itu, lakukan `configurasi` pada `nginx`

```
echo 'server {
        listen 80;

        root /var/www/laravel-praktikum-jarkom/public;

        index index.php index.html index.htm;
        server_name _;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }
        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
        }
location ~ /\.ht {
        deny all;
        }
        error_log /var/log/nginx/praktikum-jarkom_error.log;
        access_log /var/log/nginx/praktikum-jarkom_access.log;
}' > /etc/nginx/sites-available/praktikum-jarkom
```

> Lakukan Testing

```
lynx riegel.canyon.a11.com
```

**Hasil**

![first](Image/14.png)

### Soal 15

> Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire. POST /auth/register

Untuk menyelesaikan soal ini, dengan cara melakukan testing menggunakan `Apache Benchmark` Sebelum dilakukan testing, dapat menggunakan bantuan file `.json` yang akan digunakan sebagai body yang akan dikirim pada endpoint `/api/auth/ register`

#### Script

```
curl -X POST riegel.canyon.a11.com/api/auth/register -H 'Content-Type: application/json' -d '{"username": "username", "password": "password"}'
```

> lakukanlah Testing

```
ab -n 100 -c 10 reigel.canyon.a11.com/api/auth/register
```

**Hasil**

![first](Image/15.%201.png)

![first](Image/15.%202.png)

### Soal 16

> Granz Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire. POST /api/auth/login

Untuk menyelesaikan soal ini, dengan cara melakukan testing menggunakan `Apache Benchmark` Sebelum dilakukan testing, dapat menggunakan bantuan file `.json` yang akan digunakan sebagai body yang akan dikirim pada endpoint `/api/auth/login`

#### Script

```
curl -X POST riegel.canyon.a11.com/api/auth/login -H 'Content-Type: application/json' -d '{"username": "username", "password": "password"}'
```

> Lakukan Testing

```
ab -n 100 -c 10 reigel.canyon.a11.com/api/auth/login
```

**Hasil**

![first](Image/16.%201.png)

![first](Image/16.%202.png)

### Soal 17

> Granz Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire. GET /api/me

Untuk menyelesaikan soal ini, dengan cara melakukan testing menggunakan `Apache Benchmark` Sebelum dilakukan testing, dapat menggunakan token otentikasi tertentu, dan menampilkan hasilnya dengan format yang mudah dibaca. Itu juga mengizinkan koneksi ke server yang aman tanpa memeriksa sertifikat SSL.

#### Script

```
ab -n 100 -c 10 -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOi8vbGFyYXZlbC9hcGkvYXV0aC9sb2dpbiIsImlhdCI6MTcwMDEzNzc5NCwiZXhwIjoxNzAwMTQxMzk0LCJuYmYiOjE3MDAxMzc3OTQsImp0aSI6IjVkSVRld1VhTVM3dUJkVlgiLCJzdWIiOiIxIiwicHJ2IjoiMjNiZDVjODk0OWY2MDBhZGIzOWU3MDFjNDAwODcyZGI3YTU5NzZmNyJ9.QYDZtO3lcQ61dKqXmPHFXdVli9ndc63V4F1GXNe-J7U" -r -k "http://riegel.canyon.a11.com/api/me"
```

**Hasil**

![first](Image/17.png)

### Soal 18

> Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern

Mempersiapkan setup awal terlebih dahulu. Setelah tahap itu, karena hanya diberikan perintah untuk menjalankan tiga worker secara adil, kami menghadirkan implementasi `Load Balancing `dengan konfigurasi `nginx`

#### Script

```
echo 'upstream laravel {
least_conn;
server 192.174.4.1; #IP Fern
server 192.174.4.2; #IP Flamme
server 192.174.4.3; #IP Frieren
}
server {
        listen 80;
        server_name riegel.canyon.a11.com;

        location / {
        proxy_bind 192.174.2.2;
        proxy_pass http://laravel;
        }
        location /fern/ {
                proxy_bind 192.174.2.2;
                proxy_pass http://192.174.4.1/;
        }
        location /flamme/ {
                proxy_bind 192.174.2.2;
                proxy_pass http://192.174.4.2/;
        }
        location /frieren/ {
                proxy_bind 192.174.2.2;
                proxy_pass http://192.174.4.3/;
        }
}'> /etc/nginx/sites-available/riegel.canyon.a11

ln -s /etc/nginx/sites-available/riegel.canyon.a11 /etc/nginx/sites-enabled

service nginx restart
```

Lakukan perintah,

```
lynx riegel.canyon.a11/worker yang di inginkan/
```

> Lakukan Testing

sebagai contoh

```
lynx riegel.canyon.a11/fern/

lynx riegel.canyon.a11/flamme/
```

**Hasil**

![first](Image/18.%201.png)

![first](Image/18.%202.png)

![first](Image/18.%20hasil.png)

### Soal 19

> Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan
>
> - pm.max_children
> - pm.start_servers
> - pm.min_spare_servers
> - pm.max_spare_servers

> sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.

`pm.max_children` menetapkan batas jumlah pekerja PHP (proses anak) yang dapat beroperasi secara simultan. Disarankan untuk disesuaikan dengan kapasitas sumber daya server agar dapat menangani permintaan tanpa memberatkan server atau kehabisan sumber daya.

`pm.start_servers` menentukan jumlah pekerja PHP yang secara otomatis akan diinisialisasi ketika PHP-FPM pertama kali berjalan atau direstart. Pengaturan ini membantu mengoptimalkan kinerja server saat pertama kali dimulai.

`pm.min_spare_servers` menentukan jumlah minimum pekerja PHP yang tetap beroperasi saat server berjalan. Hal ini memastikan responsifitas server terhadap permintaan, bahkan saat lalu lintas rendah.

`pm.max_spare_servers` menetapkan jumlah maksimum pekerja PHP yang dapat berjalan tetapi tidak menangani permintaan. Angka ini harus diatur dengan bijak untuk menangani lonjakan lalu lintas tanpa menambahkan beban berlebihan pada saat beban rendah.

Akan ada `3 konfigurasi` yang akan diujicobakan pada proses manajemen paket pada masing-masing worker untuk pengujian.

Pada Fern, Frieren, Flamme, buka file `/etc/php/8.0/fpm/pool.d/www.conf` jadi seperti
berikut kemudian ganti `nilai atribut` pm nya

#### Script Percobaan 1

```
[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
pm = dynamic
pm.max_children = 75
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20

service php8.0-fpm restart
```

#### Script Percobaan 2

```
[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
pm = dynamic
pm.max_children = 40
pm.start_servers = 10
pm.min_spare_servers = 3
pm.max_spare_servers = 20

service php8.0-fpm restart
```

#### Script Percobaan 3

```
[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
pm = dynamic
pm.max_children = 40
pm.start_servers = 30
pm.min_spare_servers = 3
pm.max_spare_servers = 30

service php8.0-fpm restart
```

> Lakukan Testing

```
ab -n 100 -c 10 http://riegel.canyon.a11.com/
```

**Hasil Percobaan 1**

![first](Image/19.%20Percobaan1.png)

**Hasil Percobaan 2**

![first](Image/19.%20Percobaan2.png)

**Hasil Percobaan 3**

![first](Image/19.%20Percobaan3.png)

### Soal 20

> Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second.

Untuk mnyelesaikan soal ini, dapat ditambahkan `least_conn;` di baris pertama setelah `upstream laravel {...`

#### Script

```
echo 'upstream laravel {
least_conn;
server 192.174.4.1; #IP Fern
server 192.174.4.2; #IP Flamme
server 192.174.4.3; #IP Frieren
}
server {
        listen 80;
        server_name riegel.canyon.a11.com;

        location / {
        proxy_bind 192.174.2.2;
        proxy_pass http://laravel;
        }
        location /fern/ {
                proxy_bind 192.174.2.2;
                proxy_pass http://192.174.4.1/;
        }
        location /flamme/ {
                proxy_bind 192.174.2.2;
                proxy_pass http://192.174.4.2/;
        }
        location /frieren/ {
                proxy_bind 192.174.2.2;
                proxy_pass http://192.174.4.3/;
        }
}'> /etc/nginx/sites-available/riegel.canyon.a11
```

Lalu `set up` pada `package manager`

```
pm.max_children = 75
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20
```

> Lakukan Testing

```
ab -n 100 -c 10 http://riegel.canyon.a11.com/
```

**Hasil**

![first](Image/20%20fpm.png)

### Laporan Grimoire
Adapun hasil **Testing, Grafik serta Analisis** dari Grimoire dapat diakses melalui link berikut https://its.id/m/Laporan-A11_Grimoire
