---
title: "Memantau Aktivitas Server FreeBSD Dengan Perintah ps"
date: 2025-05-24T12:45:15+08:00
tags: ["WebHTTPS"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "Perintah ps digunakan untuk melihat proses yang sedang berjalan pada sistem operasi. Perintah ini sangat membantu seorang administrator sistem untuk mengetahui proses apa saja yang sedang berjalan dan apa saja yang dilakukan pada sistem operasi, berapa banyak memori yang digunakan, berapa banyak ruang CPU yang terisi, ID pengguna, nama perintah, dan lain sebagainya."
summary: "Perintah ps digunakan untuk melihat proses yang sedang berjalan pada sistem operasi. Perintah ini sangat membantu seorang administrator sistem untuk mengetahui proses apa saja yang sedang berjalan dan apa saja yang dilakukan pada sistem operasi, berapa banyak memori yang digunakan, berapa banyak ruang CPU yang terisi, ID pengguna, nama perintah, dan lain sebagainya."
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Bagi seseorang yang berkecimpung di bidang administrasi jaringan dan sistem, menguasai perintah ps utility sangatlah penting. Sebab, dengan bantuannya, pengguna dapat mengetahui banyak informasi bermanfaat tentang proses yang berjalan pada suatu sistem operasi seperti FreeBSD.


Perintah ps digunakan untuk melihat proses yang sedang berjalan pada sistem operasi. Perintah ini sangat membantu seorang administrator sistem untuk mengetahui proses apa saja yang sedang berjalan dan apa saja yang dilakukan pada sistem operasi, berapa banyak memori yang digunakan, berapa banyak ruang CPU yang terisi, ID pengguna, nama perintah, dan lain sebagainya.


## 1. Apa itu Perintah ps
Perintah ps menampilkan pilihan terperinci dari proses yang sedang berjalan. Jika administrator jaringan menginginkan pembaruan pilihan berulang dan informasi yang ditampilkan, maka ia dapat menggunakan perintah ps untuk pekerjaan tersebut.

Perintah ps merupakan salah satu utilitas yang paling banyak digunakan di FreeBSD. Utilitas ps menyediakan snapshot dari proses yang sedang berlangsung dan statusnya. Ini berguna dalam memantau proses yang sedang berjalan, mengidentifikasi process ID (PID), terminal type (TTY), penggunaan waktu CPU, nama perintah, user ID, dan informasi lainnya. Artikel ini menyediakan ikhtisar komprehensif dari berbagai kasus penggunaan perintah ps di dunia nyata.

Perintah ps mendukung tiga gaya sintaksis yang berbeda. Berikut ini adalah versi perintah ps dari tiga opsi tersebut:
- Opsi UNIX dapat digabungkan dan harus diikuti oleh tanda hubung.
- Opsi panjang GNU diikuti oleh dua tanda hubung.
- Opsi BSD dapat digabungkan dan tidak boleh digunakan dengan tanda hubung.

Berbagai jenis opsi dapat dicampur secara bebas, tetapi bentrokan dapat terjadi. Ada beberapa opsi yang sinonim dan identik secara fungsional, karena beberapa implementasi dan standar ps yang kompatibel dengan perintah ps.

## 2. Implementasi Perintah ps
Perintah `"ps"` di FreeBSD merupakan singkatan dari `"process status"`, singkatan dari nama lengkapnya. Anda dapat menggunakannya untuk mempelajari lebih lanjut tentang apa yang terjadi dalam proses latar belakang sistem Anda. Bergantung pada parameter input, perintah ini dapat menghasilkan hasil yang berbeda. Namun, tutorial ini akan menggunakan contoh ilustrasi untuk mengajarkan Anda cara menggunakan perintah `"ps"` di FreeBSD.

Perintah `"ps"` memiliki parameter tertentu yang dapat ditemukan dalam dokumentasi `"help"`. Namun, perintah ini dapat dijalankan secara independen tanpa kesalahan.

Berikut ini adalah baris Header dari perintah ps:
%CPU: Menunjukkan berapa banyak proses yang menggunakan CPU.
%MEM: Menunjukkan berapa banyak proses yang menggunakan memori.
ADDR: Menampilkan alamat memori suatu proses.
CP or C: Menampilkan informasi penjadwalan dan penggunaan CPU.
COMMAND*: Menampilkan nama proses, termasuk argumen jika tersedia.
NI: Menunjukkan nilai yang bagus.
F: Menampilkan pilihan opsi.
PID: Menampilkan nomor ID Proses.
PPID: Menampilkan nomor proses induk dari proses.
PRI: Menampilkan prioritas proses.
RSS: Singkatan untuk Ukuran set Residen
STAT or S: Menampilkan kode status proses yang sedang berlangsung.
STIME or START: Menampilkan waktu mulai proses.
TIME: Menampilkan jumlah waktu CPU yang digunakan oleh suatu proses.
VSZ: Menampilkan memori virtual yang digunakan.
TTY or TT: Menampilkan terminal yang sesuai dengan proses.
USER or UID: Menampilkan nama pengguna dan pemilik proses saat ini.
WCHAN: Menunjukkan alamat memori di mana pemrosesan sedang tertunda.

## 3. Contoh Penggunaan Perintah ps
### a. Menampilkan semua proses

```console
root@ns1:~ # ps
 PID TT  STAT    TIME COMMAND
 816 v0- I    0:00.20 /usr/local/bin/GoBlog
2085 v0- S    0:00.40 tor --DataDirectory /tmp/data-dir-1382725963 --Co
2134 v0  Is+  0:00.00 /usr/libexec/getty Pc ttyv0
2135 v1  Is+  0:00.01 /usr/libexec/getty Pc ttyv1
2136 v2  Is+  0:00.00 /usr/libexec/getty Pc ttyv2
2137 v3  Is+  0:00.00 /usr/libexec/getty Pc ttyv3
2138 v4  Is+  0:00.00 /usr/libexec/getty Pc ttyv4
2139 v5  Is+  0:00.00 /usr/libexec/getty Pc ttyv5
2140 v6  Is+  0:00.00 /usr/libexec/getty Pc ttyv6
2141 v7  Is+  0:00.00 /usr/libexec/getty Pc ttyv7
2159  0  Ss   0:00.02 -csh (csh)
2162  0  R+   0:00.00 ps
```


### b. Menampilkan proses yang sedang berjalan

```console
root@ns1:~ # ps -e
 PID TT  STAT    TIME COMMAND
 816 v0- I    0:00.21 LANG=C.UTF-8 PATH=/sbin:/bin:/usr/sbin:/usr/bin:/
2085 v0- S    0:00.40 LANG=C.UTF-8 PATH=/sbin:/bin:/usr/sbin:/usr/bin:/
2134 v0  Is+  0:00.00 TERM=xterm /usr/libexec/getty Pc ttyv0
2135 v1  Is+  0:00.01 TERM=xterm /usr/libexec/getty Pc ttyv1
2136 v2  Is+  0:00.00 TERM=xterm /usr/libexec/getty Pc ttyv2
2137 v3  Is+  0:00.00 TERM=xterm /usr/libexec/getty Pc ttyv3
2138 v4  Is+  0:00.00 TERM=xterm /usr/libexec/getty Pc ttyv4
2139 v5  Is+  0:00.00 TERM=xterm /usr/libexec/getty Pc ttyv5
2140 v6  Is+  0:00.00 TERM=xterm /usr/libexec/getty Pc ttyv6
2141 v7  Is+  0:00.00 TERM=xterm /usr/libexec/getty Pc ttyv7
2159  0  Ss   0:00.02 USER=root LOGNAME=root HOME=/root PATH=/sbin:/bin
2163  0  R+   0:00.00 USER=root LOGNAME=root HOME=/root PATH=/sbin:/bin
```

### c. Melihat proses yang tidak memiliki terminal pengontrol

```console
root@ns1:~ # ps -ax
 PID TT  STAT     TIME COMMAND
   0  -  DLs   0:01.26 [kernel]
   1  -  ILs   0:00.04 /sbin/init
   2  -  DL    0:00.00 [KTLS]
   3  -  DL    0:00.00 [crypto]
   4  -  DL    0:00.05 [cam]
   5  -  DL    0:00.14 [zfskern]
   6  -  DL    0:00.01 [rand_harvestq]
   7  -  DL    0:00.05 [pagedaemon]
   8  -  DL    0:00.00 [vmdaemon]
   9  -  DL    0:00.00 [bufdaemon]
  10  -  DL    0:00.00 [audit]
  11  -  RNL  22:06.59 [idle]
  12  -  WL    0:00.76 [intr]
  13  -  DL    0:00.00 [geom]
  14  -  DL    0:00.00 [sequencer 00]
  15  -  DL    0:00.01 [usb]
  16  -  DL    0:00.01 [acpi_thermal]
  17  -  DL    0:00.00 [vnlru]
  18  -  DL    0:00.00 [syncer]
 536  -  Is    0:00.00 /sbin/devd
 732  -  Is    0:00.01 /usr/sbin/syslogd -s
 814  -  Is    0:00.00 sshd: /usr/local/sbin/sshd [listener] 0 of 10-10
 832  -  Ss    0:00.03 /usr/sbin/ntpd -p /var/db/ntp/ntpd.pid -c /etc/n
 866  -  Ss    0:00.00 /usr/sbin/cron -s
 878  -  Is    0:00.05 /bin/sh /usr/local/bin/mysqld_safe --defaults-ex
2082  -  I     0:03.43 /usr/local/libexec/mysqld --defaults-extra-file=
2103  -  Ss    0:00.11 redis-server: /usr/local/bin/redis-server 127.0.
2122  -  Ss    0:00.02 /usr/local/sbin/httpd
2142  -  I     0:00.00 /usr/local/sbin/httpd
2143  -  S     0:00.00 /usr/local/sbin/httpd
2144  -  I     0:00.00 /usr/local/sbin/httpd
2145  -  I     0:00.00 /usr/local/sbin/httpd
2146  -  I     0:00.00 /usr/local/sbin/httpd
2156  -  Ss    0:00.03 sshd: root@pts/0 (sshd)
 816 v0- I     0:00.25 /usr/local/bin/GoBlog
2134 v0  Is+   0:00.00 /usr/libexec/getty Pc ttyv0
2135 v1  Is+   0:00.01 /usr/libexec/getty Pc ttyv1
2136 v2  Is+   0:00.00 /usr/libexec/getty Pc ttyv2
2137 v3  Is+   0:00.00 /usr/libexec/getty Pc ttyv3
2138 v4  Is+   0:00.00 /usr/libexec/getty Pc ttyv4
2139 v5  Is+   0:00.00 /usr/libexec/getty Pc ttyv5
2140 v6  Is+   0:00.00 /usr/libexec/getty Pc ttyv6
2141 v7  Is+   0:00.00 /usr/libexec/getty Pc ttyv7
2159  0  Ss    0:00.03 -csh (csh)
2175  0  R+    0:00.00 ps -ax
```


### d. Menampilkan semua info rinci dari proses yang sedang berjalan

```console
root@ns1:~ # ps auwwx
```


### e. Menunjukkan proses yang paling aktif

```console
root@ns1:~ # ps -aux | head -5
USER   PID  %CPU %MEM     VSZ    RSS TT  STAT STARTED     TIME COMMAND
root    11 200.0  0.0       0     32  -  RNL  17:21   35:09.10 [idle]
root     0   0.0  0.1       0   1104  -  DLs  17:21    0:01.31 [kernel]
root     1   0.0  0.1   11768   1164  -  ILs  17:21    0:00.04 /sbin/init
root     2   0.0  0.0       0     32  -  DL   17:21    0:00.00 [KTLS]
```


### f. Menampilkan proses pengguna root

```console
root@ns1:~ # ps -aux | grep root
root    11 200.0  0.0       0     32  -  RNL  17:21   39:52.35 [idle]
root     0   0.0  0.1       0   1104  -  DLs  17:21    0:01.33 [kernel]
root     1   0.0  0.1   11768   1164  -  ILs  17:21    0:00.04 /sbin/init
root     2   0.0  0.0       0     32  -  DL   17:21    0:00.00 [KTLS]
root     3   0.0  0.0       0     48  -  DL   17:21    0:00.00 [crypto]
root     4   0.0  0.0       0     32  -  DL   17:21    0:00.06 [cam]
root     5   0.0  0.0       0    704  -  DL   17:21    0:00.15 [zfskern]
root     6   0.0  0.0       0     16  -  DL   17:21    0:00.02 [rand_harvestq]
root     7   0.0  0.0       0     48  -  DL   17:21    0:00.10 [pagedaemon]
root     8   0.0  0.0       0     16  -  DL   17:21    0:00.00 [vmdaemon]
root     9   0.0  0.0       0     32  -  DL   17:21    0:00.00 [bufdaemon]
root    10   0.0  0.0       0     16  -  DL   17:21    0:00.00 [audit]
root    12   0.0  0.0       0    352  -  WL   17:21    0:01.19 [intr]
root    13   0.0  0.0       0     48  -  DL   17:21    0:00.00 [geom]
root    14   0.0  0.0       0     16  -  DL   17:21    0:00.00 [sequencer 00]
root    15   0.0  0.0       0    160  -  DL   17:21    0:00.01 [usb]
root    16   0.0  0.0       0     16  -  DL   17:21    0:00.01 [acpi_thermal]
root    17   0.0  0.0       0     16  -  DL   17:21    0:00.00 [vnlru]
root    18   0.0  0.0       0     16  -  DL   17:21    0:00.00 [syncer]
root   536   0.0  0.1   11568   1572  -  Is   17:21    0:00.00 /sbin/devd
root   732   0.0  0.2   12868   2764  -  Ss   17:21    0:00.01 /usr/sbin/syslogd -s
root   814   0.0  0.4   21072   7584  -  Is   17:21    0:00.00 sshd: /usr/local/sbin/sshd [listener] 0 of 10-100 startups (sshd)
root   866   0.0  0.1   12908   2544  -  Ss   17:21    0:00.00 /usr/sbin/cron -s
root  2122   0.0  0.9   37592  15768  -  Ss   17:21    0:00.03 /usr/local/sbin/httpd
root  2156   0.0  0.5   21440   8792  -  Ss   17:23    0:00.04 sshd: root@pts/0 (sshd)
root   816   0.0  2.5  803336  44164 v0- I    17:21    0:00.30 /usr/local/bin/GoBlog
root  2134   0.0  0.1   12836   2348 v0  Is+  17:21    0:00.00 /usr/libexec/getty Pc ttyv0
root  2135   0.0  0.1   12836   2356 v1  Is+  17:21    0:00.01 /usr/libexec/getty Pc ttyv1
root  2136   0.0  0.1   12836   2348 v2  Is+  17:21    0:00.00 /usr/libexec/getty Pc ttyv2
root  2137   0.0  0.1   12836   2348 v3  Is+  17:21    0:00.00 /usr/libexec/getty Pc ttyv3
root  2138   0.0  0.1   12836   2344 v4  Is+  17:21    0:00.00 /usr/libexec/getty Pc ttyv4
root  2139   0.0  0.1   12836   2340 v5  Is+  17:21    0:00.00 /usr/libexec/getty Pc ttyv5
root  2140   0.0  0.1   12836   2348 v6  Is+  17:21    0:00.00 /usr/libexec/getty Pc ttyv6
root  2141   0.0  0.1   12836   2352 v7  Is+  17:21    0:00.00 /usr/libexec/getty Pc ttyv7
root  2159   0.0  0.3   16496   4616  0  Ss   17:23    0:00.04 -csh (csh)
root  2204   0.0  0.2   13444   3120  0  R+   17:41    0:00.00 ps -aux
root  2205   0.0  0.1   12812   2420  0  S+   17:41    0:00.00 grep root
```


### g. Menampilkan proses cron

```console
root@ns1:~ # ps aux | grep cron
root   866   0.0  0.1   12908   2544  -  Is   17:21    0:00.00 /usr/sbin/cron -s
root  2211   0.0  0.1   12812   2428  0  S+   17:43    0:00.00 grep cron
```

Selain perintah di atas, ada banyak sekali pilihan ps, di bawah ini kami akan memberikan contoh beberapa pilihan dari perintah ps yang bisa Anda coba.

```console
root@ns1:~ # ps aux | less
```

```console
root@ns1:~ # ps -x
```

```console
root@ns1:~ # ps -t pts/0
```

```console
root@ns1:~ # ps -fL -C sshd
```

```console
root@ns1:~ # ps -eo pid,ppid,user
```

```console
root@ns1:~ # ps -A | grep -i sshd
 814  -  Is    0:00.00 sshd: /usr/local/sbin/sshd [listener] 0 of 10-100 startups (sshd)
2156  -  Ss    0:00.04 sshd: root@pts/0 (sshd)
2236  0  S+    0:00.00 grep -i sshd
```

Perintah ps adalah alat yang sangat ampuh untuk mengelola proses di FreeBSD. Apakah Anda ingin memantau proses tertentu, menemukan proses yang menghabiskan terlalu banyak memori atau CPU, atau memantau semua proses yang berjalan di sistem Anda, perintah ps menyediakan informasi yang Anda butuhkan. Ini adalah alat penting bagi setiap administrator FreeBSD.
