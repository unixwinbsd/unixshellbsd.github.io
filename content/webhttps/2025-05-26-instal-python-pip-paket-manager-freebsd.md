---
title: "Cara Menginstal Manajer Paket Python PIP di FreeBSD 14
date: 2025-05-26T07:25:15+08:00
tags: ["WebHTTPS"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "pip adalah pengelola paket untuk Python yang memungkinkan Anda menginstal pustaka dan paket tambahan yang bukan bagian dari pustaka Python standar seperti yang ditemukan dalam Indeks Paket Python"
summary: "pip adalah pengelola paket untuk Python yang memungkinkan Anda menginstal pustaka dan paket tambahan yang bukan bagian dari pustaka Python standar seperti yang ditemukan dalam Indeks Paket Python"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Jika boleh dikatakan, Python adalah bahasa pemrograman paling populer yang banyak digunakan oleh para analis data. Python adalah bahasa pemrograman sumber terbuka gratis yang kuat dan stabil dengan aplikasi di banyak domain perangkat lunak, seperti pengembangan web, pengembangan game, dan, tentu saja, ilmuwan data.

Meskipun Python sendiri sudah mampu melakukan banyak hal keren, para profesional data dan pengembang perangkat lunak secara umum sering menggunakan paket tambahan yang juga dikenal sebagai pustaka untuk mempermudah pekerjaan mereka dengan Python. Paket adalah kumpulan file, modul, dan dependensi terkait yang dapat digunakan berulang kali dalam berbagai aplikasi dan masalah.

Salah satu kekuatan utama Python adalah katalog pustakanya yang luas, terdokumentasi dengan baik, dan komprehensif. Di mana pustaka tersebut dihosting, bagaimana Anda menginstal dan mengelola paket yang Anda sukai. Salah satunya adalah pip, utilitas dari Python yang dapat membantu mengelola paket distribusi dengan Python dengan tepat dan benar.

pip adalah pengelola paket untuk Python yang memungkinkan Anda menginstal pustaka dan paket tambahan yang bukan bagian dari pustaka Python standar seperti yang ditemukan dalam Indeks Paket Python. Ini adalah pengganti instalasi yang mudah. Jika versi Python Anda adalah 2.7.9 (atau lebih tinggi) atau Python 3.4 (atau lebih tinggi), maka pip sudah terinstal sebelumnya dengan Python, dalam kasus lain, Anda harus menginstalnya secara terpisah.

Dalam tutorial ini, Anda akan diperkenalkan dengan dunia paket dalam Python dan pip, penginstal paket standar untuk Python. Pip adalah alat canggih yang memungkinkan Anda memanfaatkan dan mengelola banyak paket Python yang akan Anda temui sebagai profesional data dan programmer.



## 1. Operating System:
- OS: FreeBSD 14.1-STABLE
- Hostname: ns1
- IP Address: 192.168.5.234
- Python: python: 3.11_3,2
- PIP: py311-pip


## 2. Instal Python dan Paket PIP

Ada dua cara untuk menginstal pip pada sistem FreeBSD. Dalam artikel ini, kami akan menginstal pip pada server `FreeBSD 14.1`.

```
root@ns1:~ # freebsd-version
14.1-STABLE
```

Sebelum kita mulai menginstal `pip`, kita tentukan dulu versi python yang akan digunakan, karena dalam penulisan artikel ini menggunakan `python3.1`.

```console
root@ns1:~ # cd /usr/ports/lang/python311
root@hostname1:/usr/ports/lang/python311 # make install clean
```

Perintah di atas digunakan untuk menginstal `Python`, dalam kasus ini versi yang diinstal adalah `Python39`. Selanjutnya kita menginstal `pip`.

```console
root@ns1:/usr/ports/lang/python39 # cd /usr/ports/devel/py-pip
root@ns1:/usr/ports/devel/py-pip # make install clean
```

Jika Anda ingin menggunakan paket pkg untuk instalasi `pip`, berikut adalah contohnya.

```console
root@ns1:~ # pkg install python311
root@ns1:~ # pkg install py311-pip
```



## 3. Buat Symlink PIP dan Python

Karena ada begitu banyak versi python pada sistem FreeBSD, kita harus menjadikan `python39` sebagai default yang digunakan oleh server FreeBSD 13.2 kita. Berikut cara membuat symlink `python39` pada FreeBSD dan melanjutkannya dengan me-reboot/me-restart komputer sehingga `symlink python` dapat segera digunakan.

```console
root@ns1:~ # rm -R -f /usr/local/bin/python
root@ns1:~ # ln -s /usr/local/bin/python3.11 /usr/local/bin/python
root@ns1:~ # reboot
```

Setelah itu kita lanjutkan dengan membuat `pip symlink`, caranya hampir sama seperti di atas hanya perintah scriptnya saja yang berbeda, berikut ini cara membuat `pip symlink`.

```console
root@ns1:~ # rm -R -f /usr/local/bin/pip
root@ns1:~ # ln -s /usr/local/bin/pip-3.11 /usr/local/bin/pip
root@ns1:~ # reboot
```

Setelah symlink `pip` dan python terinstal, tingkatkan `pip`.

```console
root@ns1:~ # pip install --upgrade pip
```

Setelah semuanya dikonfigurasi, kami melakukan pengujian pada `pip`, dalam pengujian ini kami menginstal program dan menghapusnya menggunakan `pip`.

```console
root@ns1:~ # pip install awscli
root@ns1:~ # pip uninstall awscli
```

Menginstal dan menggunakan pip di FreeBSD merupakan cara mudah dan efisien untuk mengelola paket Python. Dengan program ini, Anda dapat menginstal, memperbarui, dan menghapus paket yang Anda perlukan untuk proyek kerja Anda. Gunakan perintah `pip install, pip install --upgrade, dan pip uninstall` untuk mengelola paket pada sistem FreeBSD.

Setelah pip diinstal pada sistem FreeBSD, kita sekarang siap memanfaatkan `pip` sepenuhnya untuk pengembangan Python di FreeBSD.
