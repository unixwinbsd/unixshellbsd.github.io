---
title: "Cara Menggunakan Perintah ls di FreeBSD dengan Contoh"
date: 2025-05-25T12:45:15+08:00
tags: ["WebHTTPS"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "Di FreeBSD, perintah ls merupakan salah satu perintah yang paling umum digunakan. Perintah ini digunakan untuk menampilkan daftar file dan subdirektori di direktori saat ini. Jika Anda baru menggunakan baris perintah, perintah pertama yang harus Anda pelajari mungkin adalah ls"
summary: "Di FreeBSD, perintah ls merupakan salah satu perintah yang paling umum digunakan. Perintah ini digunakan untuk menampilkan daftar file dan subdirektori di direktori saat ini. Jika Anda baru menggunakan baris perintah, perintah pertama yang harus Anda pelajari mungkin adalah ls"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---
Di FreeBSD, perintah `"ls"` merupakan salah satu perintah yang paling umum digunakan. Perintah ini digunakan untuk menampilkan daftar file dan subdirektori di direktori saat ini. Jika Anda baru menggunakan baris perintah, perintah pertama yang harus Anda pelajari mungkin adalah ls. Perintah ini dapat digunakan oleh pengguna biasa maupun administrator sistem.

Kemampuan untuk melihat file apa saja yang ada di dalam direktori membuat ls begitu penting. Perintah ini akan sering digunakan untuk menampilkan isi direktori. Meskipun ini bukan perintah yang rumit, perintah ini dilengkapi dengan sejumlah opsi untuk mencantumkan file beserta informasi tambahan. Meskipun ls selalu cukup untuk mencantumkan isi, Anda mungkin akan menemukan beberapa opsi ini sangat berguna.

## A. Opsi (flags) Perintah ls di FreeBSD
Perintah ls di FreeBSD, memiliki sintaks dasar sebagai berikut:

```
ls [ Options ] [File]
```
Berikut ini adalah beberapa opsi yang sering digunakan dalam perintah ls FreeBSD:

Option    Description  
ls -a        daftar semua berkas termasuk berkas tersembunyi yang dimulai dengan '.'.
ls -d        Melihat daftar direktori - dengan ' */'.
ls -l        daftar dengan format panjang - tampilkan izin.
ls -F        Tambahkan indikator (salah satu dari */=>@|) ke entri.
ls -lh        Perintah ini akan menunjukkan ukuran berkas dalam format yang dapat dibaca manusia.
ls -r        daftar dalam urutan terbalik.
ls -i        daftar nomor inode(indeks) berkas.
ls -ltr        Lihat Urutan Keluaran Terbalik menurut Tanggal.
ls -t        urutkan menurut waktu & tanggal.
ls -n        Digunakan untuk mencetak ID grup dan ID pemilik, bukan nama mereka.
ls -m       Daftar entri yang dipisahkan oleh koma harus mengisi lebar.
ls -g        Ini memungkinkan Anda untuk mengecualikan kolom informasi pemilik dan grup.
ls -q        Paksa pencetakan karakter non-grafik dalam nama berkas sebagai karakter `?';. ls -Q Tempatkan tanda kutip ganda di sekitar nama entri.

## B. Contoh dasar perintah ls di FreeBSD
Di sini, kita akan melihat contoh dasar perintah ls di lingkungan FreeBSDbeserta semua opsi yang tersedia.

### 1. Perintah 'ls' digunakan untuk mencantumkan berkas dan direktori
Isi direktori kerja Anda saat ini, yang merupakan cara teknis untuk menyatakan direktori tempat terminal Anda berada saat ini, akan tercantum jika Anda menjalankan perintah `"ls"` tanpa opsi lebih lanjut.

```console
root@ns4:/usr/local/etc # ls
```
### 2. Menampilkan berkas dan direktori tersembunyi
Gunakan opsi -a dari perintah ls untuk menampilkan berkas dan direktori tersembunyi di direktori saat ini.

```console
root@ns4:/usr/local/etc # ls -a
```
File yang diawali dengan titik disembunyikan (.). Direktori saat ini (.) dan direktori induk (..) dapat anda lihat dengan perintah "ls -a".

### 3. Menampilkan informasi lengkap tentang berkas
Opsi `"ls -l"` menampilkan konten direktori saat ini dalam format daftar panjang, satu per baris. Baris diawali dengan izin berkas atau direktori, nama pemilik dan grup, ukuran berkas, tanggal dan waktu pembuatan/pengubahan, nama berkas/folder sebagai beberapa atribut.

```console
root@ns4:/usr/local/etc # ls -l
```

### 4. Menampilkan Klasifikasikan berkas dengan karakter khusus
Perintah ls mengkategorikan berkas menggunakan parameter -F. Ini menandakan, Direktori yang diakhiri dengan garis miring (/), Berkas yang dapat dieksekusi dengan tanda bintang (*), Tautan simbolik dengan simbol laju di belakangnya (@), FIFO dengan garis vertikal di belakangnya (|), dan
Berkas biasa yang tidak berisi apa pun.

```console
root@ns4:/usr/local/etc # ls -F
```

### 5. Menampilkan Nomor Indeks File
Untuk keperluan internal, Anda mungkin perlu mengetahui nomor indeks suatu file. Untuk menampilkan nomor indeks, gunakan opsi `"ls -i"`. Anda dapat menghapus file dengan karakter khusus dalam namanya dengan menggunakan nomor indeks.

```console
root@ns4:/usr/local/etc # ls -i
```
### 6. Melihat berkas yang terakhir diedit
Berkas yang terakhir dimodifikasi akan ditampilkan terlebih dahulu karena berkas diurutkan berdasarkan waktu modifikasi. Gunakan perintah ls dan head secara bersamaan untuk mengakses berkas yang terakhir diedit di direktori saat ini.

```console
root@ns4:~ # ls -t
root@ns4:~ # ls -t | head -1
```
### 7. Menampilkan Ukuran File dalam Format yang Dapat Dibaca Manusia
Pilihan perintah `ls` lain yang sering digunakan adalah `-h` atau `-human-readable` dan `-h` harus digunakan dengan `-l` dan `-s` untuk mencetak ukuran seperti 1K 234M 2G, dsb. Ini akan menampilkan ukuran file dalam format yang dapat dibaca manusia, bukan byte.

```console
root@ns4:~ # ls -lh
total 113258
drwxr-xr-x  3 root wheel    3B Mar  9 01:29 .bundle
drwxr-xr-x  6 root wheel    6B Mar 26 13:01 .cache
drwxr-xr-x  3 root wheel    3B Mar 26 13:23 .config
-rw-r--r--  2 root wheel  1.1K Apr 13 12:41 .cshrc
-rw-r--r--  1 root wheel  114B Apr 17 21:14 .gitconfig
drwxr-xr-x  3 root wheel    3B Apr 12 19:10 .ipython
-rw-r--r--  1 root wheel   66B Sep 12  2024 .k5login
drwxr-xr-x  4 root wheel    4B Mar 14 00:34 .local
-rw-r--r--  1 root wheel  316B Sep 12  2024 .login
drwxr-xr-x  3 root wheel    3B Apr 15 13:11 .m2
drwxr-xr-x  3 root wheel    3B Apr 13 09:14 .mono
drwxr-xr-x  4 root wheel    5B Mar 20 22:10 .npm
-rw-r--r--  2 root wheel  592B Mar 26 16:44 .profile
-rw-------  1 root wheel    0B Mar 23 12:49 .sh_history
-rw-r--r--  1 root wheel  1.1K Sep 12  2024 .shrc
drwx------  2 root wheel    6B Mar  9 23:32 .ssh
drwxr-xr-x  3 root wheel    3B Mar 26 13:00 go
drwxr-xr-x  5 root wheel    9B Apr 19 20:58 latihan
-rw-------  1 root wheel  214M Apr 12 11:21 ldconfig.core
```
Bila Anda menggunakan `ls -lh`, maka akan ditampilkan seluruh informasi mengenai nama berkas atau direktori, sedangkan bila Anda menggunakan `ls -sh`, maka akan ditampilkan hanya ukuran dan nama berkas atau direktori.

```console
root@ns4:~ # ls -sh
total 113258
     1 .bundle               5 .gitconfig            5 .login                5 .profile              1 go
     1 .cache                1 .ipython              1 .m2                   1 .sh_history           9 latihan
     1 .config               1 .k5login              1 .mono                 5 .shrc            113213 ldconfig.core
     5 .cshrc                1 .local                1 .npm                  9 .ssh
```
### 8. Menampilkan Urutan Keluaran Terbalik Berdasarkan Tanggal
Pada perintah di atas, argumen l digunakan untuk format daftar panjang, argumen `t` mengurutkan semua file dan direktori berdasarkan waktu modifikasi dan mencantumkan yang terbaru terlebih dahulu, dan argumen `r` digunakan untuk membalik urutan pengurutan.

```console
root@ns4:~ # ls -ltr
```
Akibatnya, perintah ls -ltr mencantumkan semua direktori dan nama file dengan mengurutkan tanggal yang dimodifikasi dalam urutan terbalik.

### 9. Mencantumkan semua berkas dan direktori dalam urutan terbalik
Opsi `"ls -r"` mencantumkan semua berkas dan direktori dalam urutan terbalik. Semua berkas dan direktori disusun dalam urutan abjad terbalik.

```console
root@ns4:~ # ls -r
```
### 10. Menampilkan Daftar UID dan GID dari file dan direktori
Perintah "ls -n" menampilkan UID (ID Pengguna) dan GID (ID Grup) dari setiap file dan direktori, satu per baris. Pengguna dan grup (UID dan GID) yang umum memiliki 1000, tetapi UID dan GID akar memiliki nol.

```console
root@ns4:~ # ls -n
```
### 11. Mencantumkan berkas dan direktori yang dipisahkan dengan koma
Perintah "ls -m" menampilkan semua berkas dan direktori yang dipisahkan dengan koma.

```console
root@ns4:~ # ls -m
```
### 12. Mencantumkan semua berkas dan direktori tanpa detail pemilik
Opsi `"ls -g"` mirip dengan opsi `"ls -l"`, namun opsi `-g` melewatkan detail pemilik berkas dan direktori.

```console
root@ns4:~ # ls -g
```
### 13. Menampilkan Subdirektori tanpa file lain
Perintah `"ls -d */"` ini dapat digunakan untuk menampilkan hanya subdirektori dan menyembunyikan semua file lainnya.

```console
root@ns4:~ # ls -d */
```
### 14. Menampilkan halaman Bantuan perintah ls
Dengan menggunakan `"ls --help"` ini, Anda dapat melihat panduan untuk perintah ls. Perintah ini memiliki lebih banyak opsi. Beberapa di antaranya diberikan di bawah ini sebagai referensi.

```console
root@ns4:~ # man ls
```
Dalam artikel ini, beberapa opsi untuk perintah ls dicantumkan di atas beserta contohnya. Ini adalah salah satu perintah paling sederhana di FreeBSD. Meskipun Anda familier dengan perintah ini, Anda mungkin tidak familier dengan semua keadaan yang ditentukan.
