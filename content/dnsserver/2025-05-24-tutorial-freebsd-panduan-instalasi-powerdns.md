---
title: "Tutorial FreeBSD - Panduan Instalasi PowerDNS"
date: 2025-05-23T08:57:45+08:00
tags: ["DNSServer"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "Secara resmi PowerDNS terdiri dari Authoritative Server dan Recursor. PowerDNS merupakan DNS server yang ditulis dengan bahasa C++ dan berlisensi GPL. Anda dapat menggunakan kedua fungsi PowerDNS di atas atau salah satu saja"
summary: "Secara resmi PowerDNS terdiri dari Authoritative Server dan Recursor. PowerDNS merupakan DNS server yang ditulis dengan bahasa C++ dan berlisensi GPL. Anda dapat menggunakan kedua fungsi PowerDNS di atas atau salah satu saja"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Secara resmi PowerDNS terdiri dari Authoritative Server dan Recursor. PowerDNS merupakan DNS server yang ditulis dengan bahasa C++ dan berlisensi GPL. Anda dapat menggunakan kedua fungsi PowerDNS di atas atau salah satu saja. Authoritative server akan menjawab pertanyaan seputar authoritative domain, yaitu name server. Sementara itu, fungsi Recursor akan meminta name server lain di Internet untuk mengetahui perihal query yang ditanyakan.

Penggunaan PowerDNS dapat menggunakan DNS server lain seperti Bind untuk rekursi atau menggunakan PowerDNS Recursor (pdns_recursor) yang dijalankan secara terpisah. PowerDNS yang ditulis oleh Bert Hubert merupakan produk dari perusahaan Belanda bernama powerdns.com. B.V. Saat ini komunitas PowerDNS telah menyebar luas dengan banyaknya kontributor dari komunitas Open Source.

Bagi Anda yang menggunakan sistem Unix seperti FreeBSD, OpenBSD dan lainnya, PowerDNS merupakan solusi DNS server terbaik yang menawarkan kesederhanaan, kestabilan dan kehandalan. Alih-alih menggunakan berkas teks, PowerDNS menggunakan backend basis data untuk menyimpan datanya. Server basis data yang didukung oleh PowerDNS meliputi MariaDB, MySQL, PostgreSQL, dan SQLite3.

Dalam posting ini, saya akan menunjukkan cara menginstal PowerDNS dengan server MySQL sebagai backend di FreeBSD 13.3. Saya juga membahas penggunaan pdnsutil untuk mengelola zona dan membuat nama domain, serta dig untuk memeriksa koneksi DNS dan memecahkan masalah server DNS.


![freebsd powerDNS Install](https://pbs.twimg.com/media/GsQBTgQaMAAFD_y?format=jpg&name=small)


## 1. Spesifikasi sistem
- OS: FreeBSD 13.3
- Hostname: ns3
- IP server: 192.168.5.2
- MySQL server version: mysql80-server
- CPU: AMD Phenom II X4 965 3400 MHz


## 2. Setting up FQDN
FQDN (Fully Qualified Domain Name) adalah nama yang secara unik mengidentifikasi host di Internet. FQDN harus menyertakan nama domain induk. Fully Qualified Domain Name dalam DNS harus diakhiri dengan titik yang menunjukkan nama domain root. Saat Anda mendelegasikan nama domain, name server atau NS akan dianggap sebagai FQDN, yang merupakan nama domain berkualifikasi penuh dengan titik di bagian akhir.

Sebelum Anda menetapkan FQDN pada server FreeBSD, periksa nama FQDN yang sedang digunakan dengan perintah berikut.

```console
root@ns3:~ # hostname
ns5.unixwinbsd.com
Pada perintah di atas kita dapat melihat, Hostname aktif saat ini adalah `"ns5"` dan domainnya adalah `"unixwinbsd.com"`.

Jika Anda ingin membuat nama FQDN permanen, di FreeBBSD nama FQDN ditempatkan di berkas rc.conf. Sekarang jalankan perintah berikut untuk membuat konfigurasi FQDN permanen di FreeBSD.

```console
root@ns3:~ # sysrc hostname="ns3.datainchi.com"
hostname: ns3.datainchi.com -> ns3.datainchi.com
```
Setelah itu, gunakan editor `"ee"`, buka file `/etc/hosts`, dan ketik skrip di bawah ini.

```console
root@ns3:~ # ee /etc/hosts
127.0.0.1		        localhost localhost.datainchi.com
192.168.5.2		ns3 ns3.datainchi.com
```
Berikutnya, untuk memverifikasi nama domain pada sistem FreeBSD Anda, jalankan perintah berikut.

```console
root@ns3:~ # hostname -d
root@ns3:~ # hostname -f
```

## 3. Instal PowerDNS
Setelah Anda selesai mengonfigurasi domain FQDN yang akan digunakan pada server FreeBSD Anda, lanjutkan ke langkah berikutnya, yaitu menginstal PowerDNS. Di FreeBSD, repositori PowerDNS tersedia di pengelola paket PKG dan Ports. Anda dapat menginstal PowerDNS dengan salah satu cara berikut. Dalam artikel ini, kami akan menginstal PowerDNS dengan sistem ports.

Anda dapat menginstal PowerDNS dengan dua cara berbeda: melalui pengelola paket PKG dan Ports. Dalam contoh ini, Anda akan menginstal PowerDNS melalui PKG dari repositori FreeBSD. Untuk menginstal PowerDNS di FreeBSD, ikuti Panduan ini.

```console
root@ns3:~ # cd /usr/ports/dns/powerdns
root@ns3:/usr/ports/dns/powerdns # make install clean
```
Jangan lupa, instal juga powerdns-recursor, untuk mengaktifkan PowerDNS Recursor.

```console
root@ns3:/usr/ports/dns/powerdns # cd /usr/ports/dns/powerdns-recursor
root@ns3:/usr/ports/dns/powerdns-recursor # make install clean
```
Anda juga menginstal utilitas dig, untuk memeriksa koneksi PowerDNS.

```console
root@ns3:~ # pkg install -y bind-tools
```
Jika proses instalasi selesai, lanjutkan dengan mengaktifkan PowerDNS, dengan megnetikkan skrip di bawah ini di file `/etc/rc.conf`.

```console
root@ns3:~ # ee /etc/rc.conf
pdns_enable="YES"
pdns_conf="/usr/local/etc/pdns/pdns.conf"
pdns_recursor_enable="YES"
pdns_recursor_conf="/usr/local/etc/pdns/recursor.conf"
```

## 4. Buat server PowerDNS Database
Perlu diperhatikan, PowerDNS menggunakan server database untuk menyimpan file zona. Dalam contoh ini, kami akan menggunakan database MySQL sebagai backend PowerDNS. Namun, dalam artikel ini, kami tidak membahas cara menginstal MySQL, kami hanya akan berasumsi bahwa server FreeBSD Anda telah terinstal server MySQL dan berjalan normal. Jadi, kami hanya membuat database untuk PowerDNS. Ikuti skrip perintah di bawah ini.

```console
root@ns3:~ # mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.35 Source distribution

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

root@localhost [(none)]> CREATE DATABASE pdns;
Query OK, 1 row affected (0.25 sec)

root@localhost [(none)]> CREATE USER userpdns@localhost IDENTIFIED BY 'router123';
Query OK, 0 rows affected (0.12 sec)

root@localhost [(none)]> GRANT ALL PRIVILEGES ON pdns.* TO userpdns@localhost;
Query OK, 0 rows affected (0.05 sec)

root@localhost [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.04 sec)

root@localhost [(none)]> quit;
Bye
root@ns3:~ #
```
Pada FreeBSD, berkas skema MySQL disimpan di /usr/local/share/doc/powerdns. Jalankan berkas skema dengan MySQL, seperti pada contoh di bawah ini.


```console
root@ns3:~ # mysql -u userpdns -p pdns < /usr/local/share/doc/powerdns/schema.mysql.sql
```
Anda dapat memverifikasi skema basis data MySQL yang akan digunakan untuk menyimpan zona PowerDNS.


```console
root@ns3:~ # mysqlshow -u userpdns -p pdns
Enter password:
Database: pdns
+----------------+
|     Tables     |
+----------------+
| comments       |
| cryptokeys     |
| domainmetadata |
| domains        |
| records        |
| supermasters   |
| tsigkeys       |
+----------------+
```

## 5. Menyiapkan PowerDNS
Langkah berikutnya, Anda harus mengonfigurasi PowerDNS. File konfigurasi untuk konfigurasi tersebut berada di `/usr/local/etc/pdns`. Buka file pdns.conf dan tambahkan skrip MySQL seperti pada contoh di bawah ini.

```console
root@ns3:~ # cd /usr/local/etc/pdns
root@ns3:/usr/local/etc/pdns # ee pdns.conf
allow-axfr-ips=0.0.0.0/0

allow-recursion=0.0.0.0/0

# cache-ttl	Seconds to store packets in the PacketCache
cache-ttl=20

config-dir=/usr/local/etc/pdns
control-console=no
daemon=yes
default-soa-name=ns3.datainchi.com

# default-ttl	Seconds a result is valid if not set otherwise
default-ttl=3600
guardian=no

launch=gmysql
# gmysql parameters
gmysql-host=127.0.0.1
gmysql-port=3306
gmysql-dbname=pdns
gmysql-user=userpdns
gmysql-password=router123
gmysql-dnssec=yes

local-address=127.0.0.1, 192.168.5.2
# local-ipv6=

local-port=5353
logfile=/var/log/pdns/pdns.log
loglevel=9

master=yes
max-queue-length=5000
max-tcp-connections=10
recursor=127.0.0.1

setgid=pdns
setuid=pdns

slave=yes
slave-cycle-interval=600

smtpredirector=
soa-expire-default=604800
soa-minimum-ttl=3600
soa-refresh-default=10800
soa-retry-default=3600
soa-serial-offset=0
socket-dir=/var/run/pdns

# use-logfile	Use a log file
use-logfile=yes


version-string=powerdns
webserver=yes
webserver-address=192.168.5.2
# webserver-password=
webserver-port=8081
webserver-print-arguments=yes
Setelah itu, Anda mengubah skrip file `recursor.conf`.

```console
root@ns3:/usr/local/etc/pdns # ee recursor.conf
allow-from=127.0.0.0/8, 10.0.0.0/8

hint-file=/usr/local/etc/pdns/root.zone

local-address=127.0.0.1
local-port=53

max-tcp-clients=128


setgid=pdns
setuid=pdns_recursor

socket-dir=/var/run/pdns

version-string=PowerDNS Recursor
```

Setelah itu, Anda menjalankan perintah pdns_server, untuk menghubungkan PowerDNS ke server MySQL dan memverifikasi basis data yang akan digunakan PowerDNS.

```console
root@ns3:/usr/local/etc/pdns # pdns_server --daemon=no --guardian=no --loglevel=9
```
Buat berkas log PowerDNS.

```console
root@ns3:~ # mkdir -p /var/log/pdns
root@ns3:~ # touch /var/log/pdns/pdns.log
root@ns3:~ # touch /var/log/pdns/pdns_recursor.log
```
Aktifkan berkas log di file `/etc/syslog.conf`.

```console
root@ns3:~ # ee /etc/syslog.conf
!pdns
*.*      /var/log/pdns/pdns.log

!pdns_recursor
*.*      /var/log/pdns/pdns_recursor.log
```
Berikutnya, atur rotasi berkas log, ketik skrip di bawah ini ke dalam berkas `/etc/newsyslog.conf`.

```console
root@ns3:~ # ee /etc/newsyslog.conf
/var/log/pdns/*.log			644  7	   *	@T00  GJC
Jalankan berkas log PowerDNS.

```console
root@ns3:~ # service syslogd restart
Stopping syslogd.
Waiting for PIDS: 4712.
Starting syslogd.
root@ns3:~ # service newsyslog restart
Creating and/or trimming log files.
```
Setelah proses instalasi selesai, PoerDNS secara otomatis membuat pengguna dan grup baru yang disebut `"pdns"`. Jalankan perintah chown untuk menetapkan pengguna dan grup ke PowerDNS.

```console
root@ns3:~ # chown -R pdns:pdns /var/run/pdns
root@ns3:~ # chown -R pdns:pdns /usr/local/etc/pdns/pdns.conf
root@ns3:~ # chown -R pdns_recursor:pdns /usr/local/etc/pdns/recursor.conf
root@ns3:~ # chown -R pdns:pdns /var/log/pdns
```
Langkah berikutnya, jalankan PowerDNS dengan perintah layanan.

```console
root@ns3:~ # service pdns-recursor restart
root@ns3:~ # service pdns restart
```
Untuk memeriksa apakah port 53 terbuka atau tidak, Anda dapat memeriksanya dengan perintah berikut.

```console
root@ns3:~ # sockstat -4 -p 53
```

## 6. Membuat forward zone
Sebelum Anda membuat "zona maju", unduh file root.zone terlebih dahulu. Ikuti contoh di bawah ini.

```console
root@ns3:/usr/local/etc/pdns # wget ftp://ftp.rs.internic.net/domain/root.zone.gz && gunzip root.zone.gz
```
Setelah server PowerDNS aktif dan berjalan normal di FreeBSD, langkah selanjutnya adalah membuat name server, yang dapat dilakukan dengan perintah pdnsutil. Pdnsutil adalah baris perintah untuk mengelola rekaman PowerDNS dan mengendalikan perintah DNSSEC.

```console
root@ns3:~ # pdnsutil create-zone datainchi.com ns3.datainchi.com
root@ns3:~ # pdnsutil add-record datainchi.com ns3 A 192.168.5.2
root@ns3:~ # pdnsutil list-zone datainchi.com
```
Langkah terakhir, Terakhir, jalankan perintah dig untuk memeriksa catatan A dan SOA untuk server nama `ns3.datainchi.com`.

```console
root@ns3:~ # dig ns3.datainchi.com @127.0.0.1
root@ns3:~ # dig SOA ns3.datainchi.com @127.0.0.1
```
Kita lanjutkan dengan menjalankan perintah di bawah ini untuk memeriksa konfigurasi zona pada server PowerDNS Anda. Kemudian, verifikasi daftar rekaman DNS di zona datainchi.com.

```console
root@ns3:~ # pdnsutil check-all-zones
root@ns3:~ # pdnsutil list-zone datainchi.com
```

## 7. Membuat rekaman PTR (PTR record)
Langkah terakhir adalah membuat zona terbalik atau rekaman PTR yang akan menangani penerjemahan alamat IP ke nama domain. Bagian ini adalah bagian terpenting dari konfigurasi PowerDNS. Jalankan perintah di bawah ini untuk membuat zona terbalik baru.

```console
root@ns3:/usr/local/etc/pdns # pdnsutil create-zone 5.168.192.in-addr.arpa ns3.datainchi.com
```
Tambahkan catatan untuk nama server ns3.datainchi.com ke alamat IP 192.168.5.2.


```console
root@ns3:/usr/local/etc/pdns # pdnsutil add-record 5.168.192.in-addr.arpa ns3 A 192.168.5.2
```
Berikutnya, jalankan perintah di bawah ini untuk membuat catatan PTR baru untuk setiap nama domain Anda.


```console
root@ns3:/usr/local/etc/pdns # pdnsutil add-record 5.168.192.in-addr.arpa 80 PTR ns3.datainchi.com
root@ns3:/usr/local/etc/pdns # pdnsutil add-record 5.168.192.in-addr.arpa 25 PTR ns3.datainchi.com
root@ns3:/usr/local/etc/pdns # pdnsutil add-record 5.168.192.in-addr.arpa 35 PTR ns3.datainchi.com
root@ns3:/usr/local/etc/pdns # pdnsutil add-record 5.168.192.in-addr.arpa 30 PTR ns3.datainchi.com
```
Domain Name System (DNS) sangatlah rentan. karena jika DNS sedang down maka layanan website Anda yang menggunakan DNS secara otomatis tidak dapat digunakan. PowerDNS memberikan solusi yang tepat, dengan dukungan database backend menjadikan server DNS ini sebagai pilihan utama bagi Anda yang sedang merancang server DNS untuk kebutuhan website yang handal.
