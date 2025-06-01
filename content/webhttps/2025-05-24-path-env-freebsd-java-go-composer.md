---
title: "Mengatur Jalur Path ENV Di Freebsd untuk Composer Java and Go"
date: 2025-05-24T09:15:15+08:00
tags: ["WebHTTPS"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "Dalam Lingkungan, variabel perintah shell digunakan sebagai saluran utama untuk berinteraksi dengan variabel lingkungan dalam sistem operasi. Shell akan bertindak sebagai penerjemah baris perintah, yang mengeksekusi instruksi yang dimasukkan oleh pengguna."
summary: "Dalam Lingkungan, variabel perintah shell digunakan sebagai saluran utama untuk berinteraksi dengan variabel lingkungan dalam sistem operasi. Shell akan bertindak sebagai penerjemah baris perintah, yang mengeksekusi instruksi yang dimasukkan oleh pengguna."
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Dalam variabel lingkungan FreeBSD, yang dikenal sebagai ENV, merupakan nilai dinamis yang memiliki pengaruh signifikan terhadap perilaku program dan proses sistem yang sedang berjalan. Variabel Lingkungan berfungsi sebagai sarana untuk mengirimkan informasi penting ke perangkat lunak dan membentuk cara program berinteraksi dengan lingkungan sistem. Setiap proses dalam FreeBSD selalu dikaitkan dengan serangkaian variabel lingkungan, yang memandu perilaku dan interaksinya dengan proses lain.

Dalam Lingkungan, variabel perintah shell digunakan sebagai saluran utama untuk berinteraksi dengan variabel lingkungan dalam sistem operasi. Shell akan bertindak sebagai penerjemah baris perintah, yang mengeksekusi instruksi yang dimasukkan oleh pengguna.

Implementasi variabel lingkungan pada sistem operasi FreeBSD sangatlah penting. Dengan adanya variabel lingkungan, dapat ditentukan di mana variabel dapat diakses atau ditempatkan, sehingga dapat dibedakan dengan jelas antara cakupan global dan lokal.
<br></br>

<div align="center">
    <b> <h3>
Sistem operasi FreeBSD menggunakan variabel lingkungan untuk menyimpan informasi seperti string dan angka. Variabel dapat digunakan berulang kali di seluruh kode dan Anda dapat menetapkan nilai pada variabel tersebut, serta mengedit, menimpa, dan menghapusnya.
    </h3></b>
</div><br></br>


Dalam artikel ini kita akan mempelajari variabel lingkungan pada server FreeBSD. Misalnya, kita akan menggunakan aplikasi Composer, Go, dan Java.

Anda dapat melihat variabel lingkungan dalam sistem operasi FreeBSD dengan menggunakan perintah env. Perintah ini akan menampilkan nilai saat ini untuk semua pasangan lingkungan, nama/nilai, dan lainnya.

```console
root@ns3:~ # env
USER=root
LOGNAME=root
HOME=/root
MAIL=/var/mail/root
PATH=/usr/local/.composer/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin:/root/bin
TERM=xterm
BLOCKSIZE=K
MM_CHARSET=UTF-8
LANG=C.UTF-8
SHELL=/bin/csh
SSH_CLIENT=192.168.5.54 58432 22
SSH_CONNECTION=192.168.5.54 58432 192.168.5.2 22
SSH_TTY=/dev/pts/0
HOSTTYPE=FreeBSD
VENDOR=amd
OSTYPE=FreeBSD
MACHTYPE=x86_64
SHLVL=1
PWD=/root
GROUP=wheel
HOST=ns3
REMOTEHOST=192.168.5.54
JAVA_VERSION=17.0+
ES_JAVA_HOME=/usr/local/openjdk17
GO_VERSION=1.20+
GOROOT=/usr/local/go120
GOPATH=/usr/local/go120/pkg
COMPOSER_VERSION=2.7+
COMPOSER_HOME=/usr/local/.composer/cache
COMPOSER_MIRROR_PATH_REPOS=/usr/local/.composer
COMPOSER_BINARY=/usr/local/.composer
EDITOR=vi
PAGER=less
```

## 1. Mengatur Jalur Path Java (Java Path Environment Variable)
Pada FreeBSD, variabel lingkungan JAVA_HOME dan PATH perlu diatur untuk menggunakan alat seperti javac, java, Tomcat, Solr, dan lainnya. Jika Anda menggunakan `Java17` atau versi lain, secara default, FreeBSD menyimpan berkas proses instalasi di `/usr/local/openjdk17`. Variabel lingkungan JAVA_HOME menunjuk ke direktori instalasi JDK. Untuk menggunakan Java Paths dengan benar, buka berkas `/etc/csh.cshrc`, dan ketik skrip path berikut.

```console
root@ns3:~ # ee /etc/csh.cshrc
setenv JAVA_VERSION "17.0+"
setenv ES_JAVA_HOME /usr/local/openjdk17
```
Perintah di atas hanya membuat Java Path sementara, sekarang kita akan membuat perubahan Java Path permanen dengan mengedit file `~/.cshrc`.

```console
root@ns3:~ # ee .cshrc
set path = ($ES_JAVA_HOME/bin /sbin /bin /usr/sbin /usr/bin /usr/local/sbin /usr/local/bin $HOME/bin)
```
## 2. Mengatur Jalur Path GO (Go Path Environment Variable)
Di bagian ini, kami berasumsi bahwa FreeBSD Anda telah menginstal go120. Secara default, FreeBSD menyimpan semua berkas instalasi Go di direktori `/usr/local/go120`. Kami akan menggunakan direktori ini sebagai jalur untuk menyimpan semua berkas pustaka Go dari proses instalasi dan pembuatan aplikasi yang berjalan dengan Go.

Langkah-langkahnya hampir sama dengan membuat Jalur Java. Buka berkas `/etc/csh.cshrc`, yang membedakan hanya isi skripnya.

```console
root@ns3:~ # ee /etc/csh.cshrc
setenv GO_VERSION "1.20+"
setenv GOROOT /usr/local/go120
setenv GOPATH /usr/local/go120/pkg
```
Buat `GOROOT` dan `GOPATH` secara permanen pada variabel lingkungan FreeBSD. Pada file `~/.cshrc`, anda ketikkan sebagai  berikut.

```console
root@ns3:~ # ee .cshrc
set path = ($GOROOT/bin /sbin /bin /usr/sbin /usr/bin /usr/local/sbin /usr/local/bin $HOME/bin)
set path = ($GOPATH/bin /sbin /bin /usr/sbin /usr/bin /usr/local/sbin /usr/local/bin $HOME/bin)
```
Mari kita berikan contoh, perubahan Path Go. Buka direktori `/usr/local/go120/pkg`, lihat isinya dengan perintah `ls -l`.

```console
root@ns3:~ # cd /usr/local/go120/pkg
root@ns3:/usr/local/go120/pkg # ls -l
total 1
drwxr-xr-x  2 root  wheel  6 Mar 21 00:13 include
drwxr-xr-x  3 root  wheel  3 Mar 21 00:13 tool
```
Pada direktori `/usr/local/go120/pkg` terdapat dua folder, yaitu include dan tools. Untuk lebih jelasnya, bagaimana cara mengimplementasikan PATH GO, kita akan menginstal aplikasi `"Google Indexing API"`, kita akan mengkloningnya dari server Github.

```console
root@ns3:~ # cd /var
root@ns3:/var # git clone https://github.com/unixwinbsd/FreeBSD_Google_Indexing_API.git
```
Setelah Anda selesai mengkloning, lanjutkan dengan menginstal aplikasi tersebut.

```console
root@ns3:/var # cd FreeBSD_Google_Indexing_API
root@ns3:/var/FreeBSD_Google_Indexing_API # go build -v ./...
```
Sekarang Anda lihat lagi direktori `/usr/local/go120/pkg`, jalankan perintah `ls -l`.

```console
root@ns3:/var/FreeBSD_Google_Indexing_API # cd /usr/local/go120/pkg
root@ns3:/usr/local/go120/pkg # ls -l
total 2
drwxr-xr-x  2 root  wheel  6 Mar 21 00:13 include
drwxr-xr-x  3 root  wheel  3 Mar 21 00:33 pkg
drwxr-xr-x  3 root  wheel  3 Mar 21 00:13 tool
```
Dari skrip di atas, kita dapat melihat bahwa ada folder baru yang disebut `"pkg"`. Mari kita lihat isi folder pkg.

```console
root@ns3:/usr/local/go120/pkg # cd pkg
root@ns3:/usr/local/go120/pkg/pkg # ls
mod
root@ns3:/usr/local/go120/pkg/pkg # cd mod
root@ns3:/usr/local/go120/pkg/pkg/mod # ls
cache                                    go.opencensus.io@v0.24.0        google.golang.org
cloud.google.com                go.opentelemetry.io
github.com                           golang.org
```
Dengan contoh di atas, cukup jelas bahwa semua berkas instalasi akan disimpan di direktori /usr/local/go120/pkg. Pada titik ini, Anda telah mulai memahami manfaat variabel lingkungan Path.


## 3. Mengatur Jalur Path Composer (Composer Path Environment Variable)
Untuk sistem operasi apa pun yang menggunakan banyak pengguna, menjalankan aplikasi di level root tidaklah disarankan. Selain faktor keamanan, hal itu juga akan menyulitkan untuk bekerja dengan banyak pengguna. Penempatan file atau pustaka hasil instalasi aplikasi sebaiknya tidak dilakukan di root. Misalnya, pada aplikasi composer, jika Anda tidak menetapkan path, semua file akan ditempatkan di `/root/.composer/cache`.

Pada bagian ini, kita akan memindahkan path composer default ke direktori `/usr/local`, seperti yang kita lakukan pada Java dan Go. Untuk menetapkan Path Composer, caranya hampir sama dengan Java dan Go. Langkah pertama yang harus Anda lakukan adalah membuat direktori `/usr/local/.composer/cache`.

```console
root@ns3:~ # mkdir -p /usr/local/.composer/cache
root@ns3:~ # cd /usr/local/.composer/cache
root@ns3:/usr/local/.composer/cache #
```
Setelah itu, Anda buka file `/etc/csh.cshrc`, lalu ketik skrip di bawah ini.

```console
root@ns3:~ # ee /etc/csh.cshrc
setenv COMPOSER_VERSION "2.7+"
setenv COMPOSER_HOME /usr/local/.composer/cache
setenv COMPOSER_MIRROR_PATH_REPOS /usr/local/.composer
setenv COMPOSER_BINARY /usr/local/.composer
```
Dalam artikel ini kami menggunakan Composer versi 2.7.0. Setelah Anda menentukan lokasi Path Composer. Jadikan path ini permanen, sehingga semua file yang dihasilkan dari instalasi PHP Composer ditempatkan pada path yang Anda tentukan di atas. Buka file `~/.cshrc`, ketik skrip di bawah ini dalam file `~/.cshrc`.

```console
root@ns3:~ # ee .cshrc
set path = ($COMPOSER_HOME/bin /sbin /bin /usr/sbin /usr/bin /usr/local/sbin /usr/local/bin $HOME/bin)
set path = ($COMPOSER_MIRROR_PATH_REPOS/bin /sbin /bin /usr/sbin /usr/bin /usr/local/sbin /usr/local/bin $HOME/bin)
set path = ($COMPOSER_BINARY/bin /sbin /bin /usr/sbin /usr/bin /usr/local/sbin /usr/local/bin $HOME/bin)
```
Agar semua perubahan Path di atas dapat segera digunakan, boot ulang komputer Anda.

```console
root@ns3:~ # reboot
```
Setelah Anda membaca artikel ini dan menerapkannya pada server FreeBSD Anda, pengetahuan Anda tentang cara mengatur atau mengekspor variabel lingkungan di Prompt Shell csh/tcsh dari baris perintah pada sistem FreeBSD akan sangat membantu untuk menemukan lokasi setiap berkas instalasi aplikasi. Semuanya tampak rapi, karena Anda telah mengatur berkas dan direktori di tempatnya masing-masing.
