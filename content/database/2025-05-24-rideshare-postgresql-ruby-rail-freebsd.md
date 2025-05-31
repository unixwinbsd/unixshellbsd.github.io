---
title: "Menyiapkan Rideshare Dengan PostgreSQL Dan Ruby Rails di FreeBSD"
date: 2025-05-24T11:35:41+08:00
tags: ["DataBase"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "Ruby on Rails atau yang biasa disebut Rails merupakan framework full-stack yang ditulis dalam bahasa pemrograman Ruby. Ruby on Rails atau RoR tersedia untuk beberapa sistem operasi seperti FreeBSD, Linux, Windows, dan Mac OS serta backend untuk membangun situs web statis"
summary: "Ruby on Rails atau yang biasa disebut Rails merupakan framework full-stack yang ditulis dalam bahasa pemrograman Ruby. Ruby on Rails atau RoR tersedia untuk beberapa sistem operasi seperti FreeBSD, Linux, Windows, dan Mac OS serta backend untuk membangun situs web statis"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Ruby on Rails bukanlah bahasa pemrograman komputer, melainkan framework aplikasi situs web. Ruby on Rails merupakan framework yang sangat baik untuk mengembangkan aplikasi web menggunakan bahasa Ruby. Banyak orang menggunakan Ruby sebagai bahasa untuk membuat situs web statis. Dengan bantuan Ruby on Rails, Anda dapat mengembangkan aplikasi web statis lebih cepat dibandingkan dengan framework lainnya. Ruby on Rails merupakan framework web yang ramah dan mudah digunakan yang memudahkan Anda dalam mengerjakan sebagian besar pekerjaan pengembangan situs web.

Ruby on Rails atau yang biasa disebut Rails merupakan framework full-stack yang ditulis dalam bahasa pemrograman Ruby. Ruby on Rails atau RoR tersedia untuk beberapa sistem operasi seperti FreeBSD, Linux, Windows, dan Mac OS serta backend untuk membangun situs web statis.

Ruby on Rails dibuat berdasarkan serangkaian pola, pustaka, dan kerangka kerja yang telah ditetapkan sebelumnya yang memungkinkan pemula dan profesional untuk dengan cepat mengimplementasikan berbagai fungsi seperti mengirim email atau membaca data dari database MySQL, MariaDB, atau PostgreSQL. Misalnya, Rails mengimplementasikan pola object-relational mapper (ORM) yang disebut Active Record, yang memudahkan pengembang untuk berinteraksi dengan basis data menggunakan objek Ruby.

Sebelum Anda mendalami Rails, Anda perlu mengetahui sedikit tentang bahasa Ruby. Ruby adalah bahasa pemrograman open source dan tujuan umum yang digunakan untuk pengembangan situs web, otomatisasi, pemrosesan data, dan banyak lagi. Ruby merupakan bahasa pemrograman yang sangat fleksibel dan portable, artinya Anda dapat menjalankannya di hampir semua sistem operasi. Ruby hampir sama dengan Python yaitu berjalan secara dinamis dan menggunakan sintaksis yang minimalis. Singkatnya, ia menggunakan spasi untuk mengatur kode, bukan tanda kurung atau simbol lain untuk menentukan blok dalam skrip.
<br><br/>

![diagram Rideshare PostgreSQL](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/diagram%20Rideshare%20PostgreSQL.jpg)
<br><br/>


Dalam tutorial ini, kami akan menjelaskan cara menyiapkan kerangka kerja Ruby on Rails di komputer FreeBSD Anda dan menerapkan aplikasi web dengan basis data PostgreSQL. Mengapa PostgreSQL? Banyak orang menyukainya dan Anda memerlukannya, misalnya, untuk penerapan di Heroku dan lingkungan pengembangan situs web Anda.

Dalam artikel ini, kami akan menunjukkan cara menginstal Rideshare dengan Ruby on Rails dan basis data PostgreSQL. Seluruh konten artikel ini ditulis menggunakan alat-alat berikut:
- Operating system: FreeBSD 13.3
- IP Addres: 192.168.5.2
- Name server: ns3
- Ruby version: ruby 3.1.4p223 (2023-03-30 revision 957bb7cb81) [amd64-freebsd13]
- Bundler version: Bundler 2.3.6
- Gem version: gems 3.5.7
- Database: Postgresql15-server

## 1. Buat Database Rideshare
Seperti yang dijelaskan di atas, kita akan menggunakan database PostgreSQL untuk terhubung ke aplikasi Rideshare. Dalam artikel ini, kami berasumsi bahwa Anda telah menginstal database PostgeSQL. Sebelum Anda menginstal Ruby dan lainnya, pertama-tama buat database Rideshare dengan PostgreSQL. Ikuti perintah berikut untuk membuat database Rideshare.

```console
root@ns3:~ # su - postgres
$ createuser userrideshare
$ createdb rideshare -O userrideshare encoding='UTF8'
$ psql rideshare
psql (15.5)
Type "help" for help.

rideshare=# alter user userrideshare with password 'router123';
ALTER ROLE
rideshare=# exit
$ exit
root@ns3:~ #
```
Perintah di atas digunakan untuk membuat database Rideshare dengan kondisi berikut:
user: `userrideshare`
database: `rideshare`
host: `localhost`
password: `router123`

## 2. Instal Ruby on Rails
Untuk menginstal lingkungan Ruby on Rails, Anda harus menginstal Ruby di komputer FreeBSD Anda. Perlu Anda ketahui, di FreeBSD terdapat banyak versi Ruby yang akan terinstal secara otomatis, untuk mengatasinya, Anda harus menentukan versi Ruby yang akan digunakan di komputer FreeBSD. Tambahkan skrip di bawah ini ke dalam file `/etc/make.conf`.

```console
root@ns3:~ # ee /etc/make.conf
DEFAULT_VERSIONS+=ruby=3.1
```
Setelah Anda menentukan versi Ruby yang akan digunakan, yaitu Ruby31, jalankan proses instalasi Ruby.

```console
root@ns3:~ # pkg install ruby
```
Setelah itu, Anda menginstal bundler dan Gems.

```console
root@ns3:~ # pkg install rubygem-bundler ruby31-gems
```
Anda juga menginstal Rails, untuk menjalankan aplikasi Ruby.

```console
root@ns3:~ # pkg install rubygem-rails60
```

## 3. Mengkloning Rideshare Dari Github
Pada server FreeBSD, rideshare tidak tersedia di repositori atau port PKG. Anda harus mengunduhnya dari situs resmi atau server Github. Dalam artikel ini, kami akan mengkloning rideshare dari server Github. Sebelum Anda mengkloning rideshare, jalankan perintah untuk menginstal dependensi rideshare.

```console
root@ns3:~ # pkg install graphviz rubygem-ruby-graphviz rbenv
```
Setelah itu, Anda mengunduh rideshare dari server Github, kita akan menempatkan semua file rideshare di direktori `/usr/local/www`, jalankan perintah di bawah ini.

```console
root@ns3:~ # cd /usr/local/www
root@ns3:/usr/local/www # git clone https://github.com/andyatkinson/rideshare.git
```

## 4. Instal Rideshare 
Rideshare adalah aplikasi Ruby on Rails untuk buku, yang diciptakan pada tahun 2024 oleh Pragmatic Programmers. Diperlukan database PostgreSQL untuk menjalankan rideshare, karena rideshare termasuk salah satu aplikasi "High Performance PostgreSQL for Rails", yang dapat dijalankan di sistem BSD, Linux, Windows dan MaxOS.

Jalankan perintah di bawah ini untuk menginstal rideshare di FreeBSD.

```console
root@ns3:/usr/local/www/rideshare # gem update --system
root@ns3:/usr/local/www/rideshare # bundle install
root@ns3:/usr/local/www/rideshare # gem install error_highlight -v 0.4.0
root@ns3:/usr/local/www/rideshare # bundle binstubs pgslice --force
root@ns3:/usr/local/www/rideshare # bundle update
```
Setelah itu jalankan perintah import schema postgresql.

```console
root@ns3:/usr/local/www/rideshare # cd db
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d rideshare > create_role_owner.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d rideshare > create_role_app_user.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d rideshare > create_role_app_readonly.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d rideshare > alter_default_privileges_readwrite.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d rideshare > alter_default_privileges_readonly.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d alter_default_privileges_public.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d create_database.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d revoke_drop_public_schema.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d create_schema.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d create_grants_database.sql
root@ns3:/usr/local/www/rideshare/db # pg_dump -U userrideshare -W -F p -d create_grants_schema.sql
```
Jalankan migrasi Rails dengan cara standar.

```console
root@ns3:/usr/local/www/rideshare # bin/rails db:migrate
```
Jalankan Rideshare.

```console
root@ns3:/usr/local/www/rideshare # rails s -b 192.168.5.2
```
Bangun aplikasi Rails Rideshare yang lebih cepat dan lebih andal dengan memanfaatkan kemampuan PostgreSQL dan Active Record terbaik, dan gunakan keduanya untuk mengatasi tantangan skala dan pertumbuhan aplikasi Anda. Kini Anda telah berhasil menjalankan Aplikasi Rails Rideshare untuk buku "High Performance PostgreSQL for Rails" di server FreeBSD.

