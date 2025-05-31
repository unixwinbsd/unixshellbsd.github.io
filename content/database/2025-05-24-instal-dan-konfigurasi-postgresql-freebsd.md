---
title: "Cara Menginstal dan Mengkonfigurasi PostgreSQL di FreeBSD 14"
date: 2025-05-24T07:27:45+08:00
tags: ["DataBase"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "PostgreSQL awalnya bernama POSTGRES, merujuk pada asal-usulnya sebagai penerus basis data Ingres. Program PostgreSQL juga dapat digunakan sebagai penyimpanan data utama atau gudang data untuk banyak aplikasi web, seluler, geospasial, dan analitis"
summary: "PostgreSQL awalnya bernama POSTGRES, merujuk pada asal-usulnya sebagai penerus basis data Ingres. Program PostgreSQL juga dapat digunakan sebagai penyimpanan data utama atau gudang data untuk banyak aplikasi web, seluler, geospasial, dan analitis"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Postgresql adalah aplikasi sistem basis data relasional objek sumber terbuka dengan pengembangan aktif selama lebih dari 30 tahun dan telah mendapatkan reputasi baik untuk keandalan, ketahanan fitur, dan kinerja. Postgres adalah salah satu aplikasi yang menekankan ekstensibilitas dan kepatuhan SQL.

PostgreSQL bekerja berdasarkan DBMS Postgres yang dikembangkan di University of California, Berkeley. PostgreSQL ORDBMS mendukung sebagian besar fitur standar SQL dan teknologi paling modern seperti:
- Kueri Kompleks.
- Kunci asing (Foreign keys).
- Pemicu (triggers)
- Representasi.
- Transaksi.
- Kontrol konkurensi dengan multiversi.

PostgreSQL juga memungkinkan pengguna untuk menambahkan milik mereka sendiri:
- Tipe data
- Fungsi
- Operator
- Fungsi agregat
- Metode pengindeksan
- Bahasa pemrograman

PostgreSQL awalnya bernama POSTGRES, merujuk pada asal-usulnya sebagai penerus basis data Ingres. Program PostgreSQL juga dapat digunakan sebagai penyimpanan data utama atau gudang data untuk banyak aplikasi web, seluler, geospasial, dan analitis. PostgreSQL dapat menyimpan data terstruktur dan tidak terstruktur dalam satu produk.

Dalam artikel ini kami mencoba menginstal PostgreSQL pada server FreeBSD. Versi PostgreSQL yang digunakan adalah postgresql15.


![Arsitektur database PostgreSQL](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/Arsitektur%20database%20PostgreSQL.jpg)

## 1. Proses Instalasi PostgreSQL
Pada sistem FreeBSD, menginstal PostgreSQL sangatlah mudah, prosesnya hampir sama dengan menginstal program lain yang berjalan pada sistem FreeBSD. Berikut ini adalah cara menginstal klien dan server PostgreSQL.


```console
root@ns1:~ # cd /usr/ports/databases/postgresql15-client
root@ns1:/usr/ports/databases/postgresql15-client # make install clean
root@ns1:~ # cd /usr/ports/databases/postgresql15-server
root@ns1:/usr/ports/databases/postgresql15-server # make install clean
```


## 2. Proses Konfigurasi PostgreSQL
Setelah proses instalasi selesai, untuk mengaktifkan PostgreSQL kita harus mengedit berkas `/etc/rc.conf`. Ketik skrip di bawah ini di berkas /etc/rc.conf.

```console
root@ns1:~ # ee /etc/rc.conf
postgresql_enable="YES"
postgresql_class="postgres"
postgresql_data="/var/db/postgres/data15"
postgresql_flags="-w -s -m fast"
postgresql_initdb_flags="--encoding=utf-8 --lc-collate=C"
postgresql_login_class="default"
postgresql_profiles=""
```

Setelah itu kita lanjutkan dengan mengedit file /etc/login.conf, dan masukkan skrip di bawah ini.

```console
root@ns1:~ # ee /etc/login.conf
postgres:\
        :lang=en_US.UTF-8:\
        :setenv=LC_COLLATE=C:\
        :tc=default:
```

Kemudian inisialisasikan database PostgresQL. Ketik perintah berikut untuk menginisialisasi database PostgresQL.

```console
root@ns1:~ # /usr/local/etc/rc.d/postgresql initdb
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with this locale configuration:
  provider:    libc
  LC_COLLATE:  C
  LC_CTYPE:    C.UTF-8
  LC_MESSAGES: C.UTF-8
  LC_MONETARY: C.UTF-8
  LC_NUMERIC:  C.UTF-8
  LC_TIME:     C.UTF-8
The default text search configuration will be set to "english".

Data page checksums are disabled.

creating directory /var/db/postgres/data15 ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Asia/Jakarta
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

initdb: warning: enabling "trust" authentication for local connections
initdb: hint: You can change this by editing pg_hba.conf or using the option -A, or --auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    /usr/local/bin/pg_ctl -D /var/db/postgres/data15 -l logfile start
```

Setelah database PostgresQL diinisialisasi, lanjutkan dengan perintah berikut, tetapi perintah ini hanya dapat dijalankan pada pengguna Postgres, jadi Anda harus aktif pada pengguna Postgres. Ikuti langkah-langkah di bawah ini.

```console
root@ns1:~ # su postgres
$ cd /var/db/postgres
$ /usr/local/bin/pg_ctl -D /var/db/postgres/data15 -l logfile restart
waiting for server to shut down.... done
server stopped
waiting for server to start.... done
server started
$ exit
root@ns1:~ #
```

Penjelasan dari skrip di atas adalah, pada skrip pertama Anda login sebagai pengguna Postgres, skrip kedua masuk ke folder cd `/var/db/postgres` dan skrip kedua melakukan restart logfile sedangkan skrip terakhir adalah logout sebagai pengguna Postgres.

Setelah keluar dari pengguna Postgres, edit file `/var/db/postgres/data13/postgresql.conf`, dan masukkan skrip di bawah ini.

```console
root@ns1:~ # ee /var/db/postgres/data15/postgresql.conf
listen_addresses = '192.168.5.2'		# what IP address(es) to listen on;
port = 5432
```

IP untuk listen_addresses disesuaikan dengan IP Private server FreeBSD, karena pada penulisan artikel ini IP Private yang digunakan adalah 192.168.5.2, jadi listen_addresses = '192.168.5.2' dan port default PostgreSQL adalah port 5432.

Kemudian kita edit juga file `/var/db/postgres/data13/pg_hba.conf`, dan ganti host script dengan IP address yang sesuai dengan server FreeBSD, berikut contoh scriptnya.

```console
root@ns1:~ # ee /var/db/postgres/data15/pg_hba.conf
host    all             all             192.168.5.2/24            trust
```

Sesuaikan alamat IP dengan skrip di atas. Kemudian kita restart program database PostgresQL.

```console
root@ns1:~ # service postgresql restart
2023-08-21 13:43:37.306 WIB [1439] LOG:  ending log output to stderr
2023-08-21 13:43:37.306 WIB [1439] HINT:  Future log output will go to log destination "syslog".
```

```console
root@ns1:~ # service postgresql status
pg_ctl: server is running (PID: 1439)
/usr/local/bin/postgres "-D" "/var/db/postgres/data15"
```

Jika tidak ada yang salah dengan pengaturan skrip di atas dan program basis data PostgresQL seharusnya dapat berjalan di FreeBSD.


## 3. Membuat Password Postgres
Secara default, saat proses instalasi database postgresql selesai, postgresql akan secara otomatis membuat database dengan nama `"postgres"`, dan pengguna untuk database `"postgres"` diberi nama `"postgres"`. Pengguna dan database `"postgres"` belum diberi kata sandi. Berikut cara memberikan kata sandi kepada pengguna Postgres.



```console
root@ns1:~ # passwd postgres
Changing local password for postgres
New Password: router
Retype New Password: router
```

Atau Anda juga dapat membuat kata sandi Postgres dengan cara lain, seperti contoh di bawah ini.

```console
root@ns1:~ # su - postgres
$ psql -c "alter user postgres with password 'router'"
ALTER ROLE
$
```

Dalam contoh skrip di atas kami telah membuat kata sandi Postgres dengan nama `"router"`.


## 4. Cara Menggunakan Database PostgreSQL15
Setelah pengguna Postgres diberi kata sandi, sekarang kita coba membuat tabel di database `"Postgres"`. Berikut ini adalah contoh pembuatan tabel di database `"postgres"`.

```console
root@ns1:~ # su - postgres
$ psql postgres
psql (15.3)
Type "help" for help.

postgres=# CREATE TABLE person (
    person_id BIGINT,
    name VARCHAR(255),
    age INT,
    city VARCHAR(255)
);
CREATE TABLE
postgres=# INSERT INTO person VALUES (1001, 'M. Jaka Setiawan', 9, 'Tegal');
INSERT 0 1
postgres=# INSERT INTO person VALUES (1002, 'Iwan Setiawan', 42, 'Bekasi');
INSERT 0 1
postgres=# INSERT INTO person VALUES (1003, 'Siti Umaroh', 41, 'Tegal');
INSERT 0 1
postgres=# INSERT INTO person VALUES (1004, 'Kanaka Robih Setiawan', 11, 'Bekasi');
INSERT 0 1
postgres=# select * from person;
 person_id |         name          | age |  city  
-----------+-----------------------+-----+--------
      1001 | M. Jaka Setiawan      |   9 | Tegal
      1002 | Iwan Setiawan         |  42 | Bekasi
      1003 | Siti Umaroh           |  41 | Tegal
      1004 | Kanaka Robih Setiawan |  11 | Bekasi
(4 rows)

postgres=#
```

Sekarang kita coba lagi dengan membuat database, user, tabel dan password. Mari kita beri contoh dengan membuat database user `"puncakanjani"` dengan nama `"gunungrinjani"`. Ikuti langkah-langkah berikut.

```console
root@ns1:~ # su - postgres
$ createuser gunungrinjani
$ createdb puncakanjani -O gunungrinjani
$ psql puncakanjani
psql (15.3)
Type "help" for help.

puncakanjani=#
```

Deskripsi dari perintah di atas adalah.

1. Script pertama “su - postgres” masuk ke user “postgres”
2. Script ke-2, membuat user dengan nama Gunungrinjani
3. Script ke-3, membuat database Puncakanjani untuk user Gunungrinjani
4. Script ke-4, psql dimasukkan ke user Gunungrinjani
5. Script Puncakanjani=# berarti aktif di database Puncakanjani

Langkah selanjutnya adalah membuat kata sandi untuk pengguna `Gunungrinjani`.

```console
puncakanjani=# alter user gunungrinjani with password 'sembalun';
ALTER ROLE
```

Perintah di atas adalah untuk membuat password bagi pengguna `Gunungrinjani` dengan password `"sembalun"`. Setelah password berhasil dibuat, sekarang kita praktikkan cara membuat tabel. Berikut contohnya.

```console
puncakanjani=# create table tabelgunung ( id int,first_name text, last_name text );
CREATE TABLE
puncakanjani=# insert into tabelgunung (id,first_name,last_name) values (1,'gunung','argopuro');
INSERT 0 1
puncakanjani=# insert into tabelgunung (id,first_name,last_name) values (1,'gunung','rinjani');
INSERT 0 1
puncakanjani=# insert into tabelgunung (id,first_name,last_name) values (1,'gunung','raung');
INSERT 0 1
```

Lihatlah tabel yang telah kita buat di atas.

```console
puncakanjani=# select * from tabelgunung;
 id | first_name | last_name 
----+------------+-----------
  1 | gunung     | argopuro
  1 | gunung     | rinjani
  1 | gunung     | raung
(3 rows)

puncakanjani=#
```

Hapus `tabelgunung`.

```console
puncakanjani=# DROP TABLE tabelgunung;
DROP TABLE
```

Hapus database `puncakanjani`.

```console
puncakanjani=# exit
$ dropdb puncakanjani
```

Hapus user `gunungrinjani`.

```console
$ dropuser gunungrinjani
```

PostgreSQL merupakan sistem manajemen basis data relasional yang tangguh, kaya akan fitur, dan banyak digunakan dalam berbagai aplikasi. PostgreSQL menjadi pilihan yang menarik bagi banyak perusahaan, termasuk perusahaan raksasa seperti Apple, Netflix, Instagram, IMDB, dan lainnya.

Menurut data statistik, pada tahun 2023 PostgreSQL menempati peringkat keempat di dunia di antara DBMS dalam hal popularitas, di bawah Oracle, MySQL, dan Microsoft SQL Server. Selain itu, PostgreSQL merupakan DBMS open source yang paling populer.
