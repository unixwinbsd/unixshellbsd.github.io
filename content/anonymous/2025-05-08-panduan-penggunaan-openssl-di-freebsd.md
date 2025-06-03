---
title: "Panduan Lengkap Penggunaan OpenSSL di FreeBSD"
date: 2025-05-08T10:35:41+08:00
tags: ["Anonymous"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "OpenSSL menyediakan alat yang diperlukan untuk membuat permintaan penandatanganan sertifikat, kunci server pribadi, dan sertifikat yang ditandatangani sendiri. Bila dikombinasikan dengan otoritas sertifikat yang diakui, sertifikat server tepercaya dapat dibuat untuk digunakan dengan protokol TCP seperti HTTP, SMTP, IMAP, dan sebagainya."
summary: "OpenSSL menyediakan alat yang diperlukan untuk membuat permintaan penandatanganan sertifikat, kunci server pribadi, dan sertifikat yang ditandatangani sendiri. Bila dikombinasikan dengan otoritas sertifikat yang diakui, sertifikat server tepercaya dapat dibuat untuk digunakan dengan protokol TCP seperti HTTP, SMTP, IMAP, dan sebagainya."
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

OpenSSL adalah perangkat sumber terbuka dan pustaka kriptografi yang mengimplementasikan protokol SSL (Secure Sockets Layer) dan TLS (Transport Layer Security). OpenSSL adalah proyek independen yang dikelola oleh relawan dari seluruh dunia. Singkatnya, OpenSSL menyediakan perangkat kriptografi untuk mengamankan koneksi jaringan. Implementasi umum SSL adalah mengamankan halaman web dengan HTTPS (HyperText Transfer Protocol yang diamankan dengan enkripsi). Enkripsi OpenSSL melalui protokol HTTP terdiri dari langkah-langkah berikut:
- Klien HTTP (peramban web) mengirim permintaan HTTPS ke server web.
- Server merespons dengan mengirim Sertifikat SSL ke klien yang berisi kunci publik, nama domain, dan otoritas sertifikat SSL yang diterbitkan.
- Klien mengirim pesan yang dienkripsi menggunakan kunci SSL publik server.
- Server mendekripsi pesan ini dengan kunci SSL pribadinya.
- Server akhirnya mengirim kembali pesan yang didekripsi ke klien.
- Jika klien menerima pesan yang benar, kedua belah pihak dapat mulai bertukar informasi dengan aman, dengan asumsi otoritas sertifikat yang menerbitkan dipercaya oleh klien.

OpenSSL menyediakan alat yang diperlukan untuk membuat permintaan penandatanganan sertifikat, kunci server pribadi, dan sertifikat yang ditandatangani sendiri. Bila dikombinasikan dengan otoritas sertifikat yang diakui, sertifikat server tepercaya dapat dibuat untuk digunakan dengan protokol TCP seperti HTTP, SMTP, IMAP, dan sebagainya.

OpenSSL didasarkan pada pustaka SSLeay yang awalnya dikembangkan oleh Eric A. Young dan Tim J. Hudson. SSLeay adalah implementasi sumber terbuka dari protokol Secure Socket Layer Netscape, yang digunakan dalam peramban Netscape Secure Server dan Navigator pada pertengahan 1990-an.

OpenSSL sangat mudah dipasang di FreeBSD, berikut cara memasang OpenSSL di sistem FreeBSD.

```console
root@router2:~ # cd /usr/ports/security/openssl
root@router2:/usr/ports/security/openssl # make DISABLE_VULNERABILITIES=yes install clean
root@router2:/usr/ports/security/openssl # echo "DEFAULT_VERSIONS+=ssl=openssl" >> /etc/make.conf
```
Sebelum mengonfigurasi OpenSSL, buat cadangan atau salin file `openssl.cnf` sehingga jika terjadi kesalahan selama konfigurasi, file asli masih ada.

```console
root@router2:~ # cd /usr/local/openssl
root@router2:/usr/local/openssl # cp openssl.cnf openssl.cnf.backup
```
Sekarang kita dapat langsung memodifikasi file openssl.cnf, tetapi sebelum itu periksa dulu versi OpenSSL yang Anda gunakan.

```console
root@router2:/usr/local/openssl # openssl version
OpenSSL 1.1.1t-freebsd  7 Feb 2023
```

## 1. Buat Sertifikat CA
Di bagian ini, Sertifikat SSL akan dibuat menggunakan skrip `CA.pl` (skrip Perl) yang diinstal oleh port OpenSSL. Anda dapat membuat sertifikat resmi untuk menandatangani Sertifikat SSL Anda dengan mengirimkan permintaan. Otoritas sertifikat memerlukan berkas permintaan sertifikat untuk membuat Sertifikat SSL yang valid untuk server Anda.

Kami akan menggunakan skrip CA.pl yang disertakan dengan OpenSSL untuk membuat permintaan sertifikat. Skrip berikut akan menyalin berkas CA.pl ke folder `/usr/local/openssl/certs`.

```console
root@router2:~ # cd /usr/local/openssl
root@router2:/usr/local/openssl # cp misc/CA.pl certs
```
Jalankan skrip di bawah ini untuk membuat permintaan sertifikat.

```console
root@router2:~ # cd /usr/local/openssl/certs
root@router2:/usr/local/openssl/certs # setenv OPENSSL /usr/local/bin/openssl
root@router2:/usr/local/openssl/certs # ./CA.pl -newreq
Use of uninitialized value $1 in concatenation (.) or string at ./CA.pl line 133.
====
/usr/local/bin/openssl req  -new  -keyout newkey.pem -out newreq.pem -days 365
Ignoring -days; not generating a certificate
Generating a RSA private key
..........+++++
...................+++++
writing new private key to 'newkey.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:id
State or Province Name (full name) [Some-State]:jawa barat
Locality Name (eg, city) []:bekasi
Organization Name (eg, company) [Internet Widgits Pty Ltd]:mediatama
Organizational Unit Name (eg, section) []:networking
Common Name (e.g. server FQDN or YOUR name) []:router2.unixexplore.com
Email Address []:datainchi@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
==> 0
====
Request is in newreq.pem, private key is in newkey.pem
root@router2:/usr/local/openssl/certs #
Saat membuat sertifikat di atas, yang perlu diperhatikan adalah menuliskan `Common Name (mis. server FQDN atau nama ANDA)`. Pada bagian ini kita harus memasukkan nama host dan domain server FreeBSD kita, pada artikel ini `router2` adalah nama host dan `unixexplore.com` adalah nama domain.

Menjalankan skrip file `CA.pl` di atas akan menghasilkan file `newkey.pem` dan `newreq.pem`, kedua file tersebut berisi sertifikat SSL terenkripsi untuk server pribadi atau private server. Untuk memudahkan identifikasi, salin file tersebut ke `router2.unixexplore.com-encrypted-key.pem` menggunakan perintah berikut.

```console
root@router2:/usr/local/openssl/certs # cp newkey.pem router2.unixexplore.com-encrypted-key.pem
root@router2:/usr/local/openssl/certs # cp newreq.pem router2.unixexplore.com-req.pem
```
File `router2.unixexplore.com-encrypted-key.pem` dienkripsi dengan kata sandi yang Anda masukkan sebelumnya. Penting bagi Anda untuk selalu mengingat kata sandi tersebut. Anda harus memasukkannya saat aplikasi SSL menggunakannya.

Jika file ini akan digunakan pada server tanpa pengawasan, mungkin ide yang sangat bagus untuk mendekripsi file tersebut, sehingga daemon dapat membacanya tanpa campur tangan pengguna. Untuk menghapus enkripsi dan membuat file tidak terenkripsi yang hanya dapat dibaca oleh root, gunakan skrip berikut.

```console
root@router2:/usr/local/openssl/certs # openssl rsa -in router2.unixexplore.com-encrypted-key.pem -out router2.unixexplore.com-unencrypted-key.pem
Enter pass phrase for router2.unixexplore.com-encrypted-key.pem:
writing RSA key
root@router2:/usr/local/openssl/certs # chmod 400 router2.unixexplore.com-unencrypted-key.pem
```

## 2. Buat Sertifikat SSL yang Ditandatangani Sendiri
Salah satu cara untuk membuat sertifikat Anda sendiri adalah dengan menggunakan berkas CA.pl. Perlu diperhatikan bahwa membuat sertifikat Anda sendiri akan menyebabkan kotak dialog Sertifikat Tidak Tepercaya muncul di klien aplikasi (peramban web, klien email, dll.).

Anda dapat memasang berkas sertifikat server Anda di sistem klien untuk menghindari hal ini. Berikut ini adalah skrip untuk membuat sertifikat SSL Anda sendiri dengan masa berlaku 3 tahun.

```console
root@router2:~ # cd /usr/local/openssl
root@router2:/usr/local/openssl # cp misc/CA.pl certs
root@router2:/usr/local/openssl # sed -I .old 's/365/1095/' openssl.cnf
```
Jalankan skrip berikut untuk membuat otoritas sertifikat SSL Anda sendiri.

```console
root@router2:~ # cd /usr/local/openssl/certs
root@router2:/usr/local/openssl/certs # setenv OPENSSL /usr/local/bin/openssl
root@router2:/usr/local/openssl/certs # ./CA.pl -newca
CA certificate filename (or enter to create)

Making CA certificate ...
====
/usr/local/bin/openssl req  -new -keyout ./demoCA/private/cakey.pem -out ./demoCA/careq.pem
Generating a RSA private key
.........+++++
...................+++++
writing new private key to './demoCA/private/cakey.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:id
State or Province Name (full name) [Some-State]:jawa barat
Locality Name (eg, city) []:bekasi
Organization Name (eg, company) [Internet Widgits Pty Ltd]:mediatama
Organizational Unit Name (eg, section) []:networking
Common Name (e.g. server FQDN or YOUR name) []:router2.unixexplore.com
Email Address []:datainchi@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
==> 0
====
====
/usr/local/bin/openssl ca  -create_serial -out ./demoCA/cacert.pem -days 1095 -batch -keyfile ./demoCA/private/cakey.pem -selfsign -extensions v3_ca  -infiles ./demoCA/careq.pem
Using configuration from /usr/local/openssl/openssl.cnf
Enter pass phrase for ./demoCA/private/cakey.pem:
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number:
            4b:b7:ad:37:c3:24:ea:c9:62:0a:1b:d5:be:ec:57:4f:e5:94:33:c8
        Validity
            Not Before: Jun 30 13:17:27 2023 GMT
            Not After : Jun 29 13:17:27 2026 GMT
        Subject:
            countryName               = id
            stateOrProvinceName       = jawa barat
            organizationName          = mediatama
            organizationalUnitName    = networking
            commonName                = router2.unixexplore.com
            emailAddress              = datainchi@gmail.com
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                DA:90:FF:39:70:F4:F3:93:E8:CF:29:6E:35:BE:0C:74:EB:38:89:CB
            X509v3 Authority Key Identifier:
                keyid:DA:90:FF:39:70:F4:F3:93:E8:CF:29:6E:35:BE:0C:74:EB:38:89:CB

            X509v3 Basic Constraints: critical
                CA:TRUE
Certificate is to be certified until Jun 29 13:17:27 2026 GMT (1095 days)

Write out database with 1 new entries
Data Base Updated
==> 0
====
CA certificate is in ./demoCA/cacert.pem
root@router2:/usr/local/openssl/certs #
```
Untuk membuat permintaan sertifikat di atas, gunakan perintah berikut.

```console
root@router2:/usr/local/openssl/certs #  ./CA.pl -newreq
Use of uninitialized value $1 in concatenation (.) or string at ./CA.pl line 133.
====
/usr/local/bin/openssl req  -new  -keyout newkey.pem -out newreq.pem -days 365
Ignoring -days; not generating a certificate
Generating a RSA private key
..............................................................................+++++
..................................+++++
writing new private key to 'newkey.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:id
State or Province Name (full name) [Some-State]:jawa barat
Locality Name (eg, city) []:bekasi
Organization Name (eg, company) [Internet Widgits Pty Ltd]:mediatama
Organizational Unit Name (eg, section) []:networking
Common Name (e.g. server FQDN or YOUR name) []:router2.unixexplore.com
Email Address []:datainchi@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
==> 0
====
Request is in newreq.pem, private key is in newkey.pem
root@router2:/usr/local/openssl/certs #
```
Dengan menggunakan perintah `copy`, salin semua sertifikat ke direktori `/usr/local/openssl/certs`, gunakan skrip berikut untuk melakukannya.

```console
root@router2:/usr/local/openssl/certs # cp newreq.pem router2.unixexplore.com-cert.pem
root@router2:/usr/local/openssl/certs # cp newkey.pem router2.unixexplore.com-encrypted-key.pem
root@router2:/usr/local/openssl/certs # cp demoCA/cacert.pem ./unixexplore.com-CAcert.pem
root@router2:/usr/local/openssl/certs # cp demoCA/private/cakey.pem ./unixexplore.com-encrypted-CAkey.pem
```
Untuk menghapus enkripsi dan membuat file yang tidak terenkripsi hanya dapat dibaca oleh root, gunakan skrip berikut.

```console
root@router2:/usr/local/openssl/certs # openssl rsa -in router2.unixexplore.com-encrypted-key.pem -out router2.unixexplore.com-unencrypted-key.pem
Enter pass phrase for router2.unixexplore.com-encrypted-key.pem:
writing RSA key
root@router2:/usr/local/openssl/certs # chmod 400 router2.unixexplore.com-unencrypted-key.pem
```
Sekarang kita akan mengekspor sertifikat CA atau sertifikat root agar dapat diinstal pada sistem yang akan menggunakan Sertifikat SSL Anda. Hal ini diperlukan untuk menghilangkan munculnya pesan Peringatan Sertifikat SSL Root yang Tidak Dipercaya. Pesan ini muncul untuk memperingatkan pengguna akhir bahwa ada potensi masalah dengan Sertifikat SSL.

Lebih sulit untuk mendeteksi sesi SSL yang sebenarnya dibajak jika peringatan yang tidak perlu ini tidak dihapus. Sebagian besar sistem klien (Windows dan Mac OS X) mengenali file sertifikat SSL yang dikodekan dalam format biner DER (Distinguished Encoding Rules). Untuk mengonversi sertifikat PEM (Privacy Enhanced Mail) berbasis teks ke format DER, ketik perintah berikut.

```console
root@router2:/usr/local/openssl/certs # openssl x509 -in unixexplore.com-CAcert.pem -inform PEM -out unixexplore.com-CAcert.cer -outform DER
```
Anda dapat mengirim sertifikat berkode DER melalui email dengan perintah ini.

```console
root@router2:/usr/local/openssl/certs # uuencode unixexplore.com-CAcert.cer unixexplore.com-CAcert.cer | mail -s "Subject" datainchi@gmail.com
```
CAcert adalah otoritas sertifikat yang menerbitkan Sertifikat SSL gratis. Jika Anda baru mengenal Sertifikat SSL, situs ini adalah tempat yang tepat untuk mempelajari proses penandatanganan Sertifikat SSL. Anda dapat membuat permintaan penandatanganan sertifikat seperti yang dijelaskan dalam panduan ini dan ditandatangani oleh CAcert.

Sertifikat yang ditandatangani oleh CAcert memiliki dukungan terbatas; sebagian besar peramban web tidak menyertakan sertifikat root mereka dalam basis data CA tepercaya. Namun, sertifikat root CAcert disertakan dalam FreeBSD dan beberapa distribusi Linux.
