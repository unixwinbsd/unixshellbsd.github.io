---
title: "Cara Menginstal dan Mengkonfigurasi PostgreSQL di FreeBSD 14"
date: 2025-05-24T11:15:15+08:00
tags: ["DataBase"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "Untuk dapat menggunakan Git, saat ini tersedia berbagai layanan untuk menyimpan dan membagikan kode tersebut. Misalnya, GitLab, GitHub, Bitbucket, dan Gitorous. Di antara pilihan tersebut, GitLab merupakan salah satu layanan penyimpanan Git yang sedang naik daun dan memiliki banyak pengguna"
summary: "Untuk dapat menggunakan Git, saat ini tersedia berbagai layanan untuk menyimpan dan membagikan kode tersebut. Misalnya, GitLab, GitHub, Bitbucket, dan Gitorous. Di antara pilihan tersebut, GitLab merupakan salah satu layanan penyimpanan Git yang sedang naik daun dan memiliki banyak pengguna"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Gitlab merupakan layanan yang menyediakan akses jarak jauh ke repositori Git. Selain menjadi tempat penyimpanan kode Anda, layanan ini juga menyediakan fitur-fitur tambahan yang dirancang untuk membantu mengelola siklus pengembangan perangkat lunak. Git merupakan alat yang berfungsi untuk memudahkan para pengembang. Alat ini berguna sebagai Version Control System (VCS) yang bertugas untuk melacak perubahan pada berkas kode. Bagi para pengembang, keberadaan Git sangat memudahkan mereka untuk berkolaborasi guna menyelesaikan proyek dengan pengembang lain atau pada proyek mereka sendiri.

Alat-alat seperti Git memungkinkan para pengembang untuk melakukan perubahan pada kode sumber tertentu. Mereka tidak perlu khawatir akan adanya konflik dalam menggabungkan kode-kode tersebut. Sebab dengan menggunakan Git, semua perubahan pada kode sumber dapat dilacak.

Untuk dapat menggunakan Git, saat ini tersedia berbagai layanan untuk menyimpan dan membagikan kode tersebut. Misalnya, GitLab, GitHub, Bitbucket, dan Gitorous. Di antara pilihan tersebut, GitLab merupakan salah satu layanan penyimpanan Git yang sedang naik daun dan memiliki banyak pengguna.

Git adalah sistem pembuatan versi kode sumber yang memungkinkan Anda melacak perubahan secara lokal dan mengirim atau menarik perubahan dari sumber daya jarak jauh.


![proses instalasi gitlab di freebsd](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/Proses%20Install%20Gitlab%20di%20FreeBSD.jpg)


GitLab adalah layanan mirip github yang dapat digunakan organisasi untuk menyediakan manajemen internal repositori git. Ini adalah sistem manajemen repositori Git yang dihosting sendiri yang menjaga kode pengguna tetap pribadi dan dapat dengan mudah menerapkan perubahan kode.
Berikut ini adalah beberapa keuntungan menggunakan Gitlab:
Tersedia Gitlab CE yang bersifat open-source.
Pencatatan secara Realtime.
Mendukung Parallel Builds.
Memfasilitasi kolaborasi antar pengembang

## 1. Install PostgreSQL Database
Semua fitur Gitlab akan berjalan dengan baik jika Anda menggunakan database PostgreSQL. Jadi kami sarankan untuk menggunakan database PostgreSQL yang akan terhubung dengan Gitlab. Anda dapat menggunakan Database MySQL, tetapi beberapa fitur Gitlab tidak akan bekerja dengan baik.

Di FreeBSD repositori PostgreSQL tersedia, Anda dapat menginstal PostgreSQL dengan PKG atau port. Namun sebaiknya menginstal PostgreSQL dengan port sistem. Ada dua PostgreSQL yang harus Anda instal, yaitu klien PostgreSQL dan server PostgreSQL. Berikut cara menginstal PostgreSQL di FreeBSD.

```console
root@ns3:~ # cd /usr/ports/databases/postgresql15-client
root@ns3:/usr/ports/databases/postgresql15-client # make install clean
root@ns3:/usr/ports/databases/postgresql15-client # cd /usr/ports/databases/postgresql15-server
root@ns3:/usr/ports/databases/postgresql15-server # make install clean
```
In this article, we do not fully explain how to install and use PostgreSQL, you can read the previous article "Cara Menginstal dan Mengkonfigurasi PostgreSQL di FreeBSD"


## 2. Membuat PostgreSQL
Pada bagian ini kita akan membuat database yang akan digunakan oleh Gitlab untuk berkomunikasi dengan PostgreSQL. Berikut adalah database yang akan kita buat dengan PostgreSQL.

user: `usergitlab`
database: `gitlab`
host: `localhost`
password: `router123`

Jalankan perintah di bawah ini untuk membuat database PostgreSQL.

```console
root@ns3:~ # su - postgres
$ createuser usergitlab
$ createdb gitlab -O usergitlab encoding='UTF8'
$ psql gitlab
psql (15.5)
Type "help" for help.

gitlab=# alter user usergitlab with password 'router123';
ALTER ROLE
gitlab=#
```
Perintah di atas digunakan untuk menyambung ke basis data PostgreSQL, dan membuat basis data, pengguna, dan kata sandi.


## 3. Install Redis
Gitlab menggunakan Redis untuk menyimpan semua cache-nya. Tujuannya adalah untuk mempercepat kinerja Gitlab. Jalankan perintah berikut untuk menginstal Redis.

```console
root@ns3:~ # cd /usr/ports/databases/redis
root@ns3:/usr/ports/databases/redis # make install clean
```
Setelah itu, konfigurasikan Redis, buka file redis.conf dan aktifkan dengan menghilangkan tanda "#" di awal skrip. Berikut skrip yang harus Anda aktifkan.

```console
root@ns3:~ # cd /usr/local/etc
root@ns3:/usr/local/etc # ee redis.conf
unixsocket /var/run/redis/redis.sock
unixsocketperm 770
requirepass foobared
#bind 127.0.0.1 -::1
```
Izinkan Redis untuk memulai dengan menambahkan skrip di bawah ini ke file `/etc/rc.conf`.

```console
root@ns3:~ # ee /etc/rc.conf
redis_enable="YES"
```
Jalankan Redis dengan perintah restart.

```console
root@ns3:~ # service redis restart
```

## 4. Install gitlab-ce
Sebelum Anda menginstal gitlab-ce, instal dulu dependensi yang dibutuhkan oleh gitlab-ce. Berikut cara menginstal dependensi gitlab-ce.

```console
root@ns3:~ # pkg install re2 html2text rubygem-re2 rubygem-gitlab-glfm-markdown rubygem-prometheus-client-mmap gpgme rubygem-rugged rubygem-charlock_holmes rubygem-plist rubygem-nkf rubygem-rouge rubygem-wmi-lite rubygem-net-ssh rubygem-lockbox rubygem-unf_ext
```
Setelah itu, Anda dapat menjalankan perintah gitlab-ce install.

```console
root@ns3:~ # cd /usr/ports/www/gitlab-ce
root@ns3:/usr/ports/www/gitlab-ce # make install clean
```
Atau, jika Anda ingin menggunakan paket PKG, jalankan perintah di bawah ini.

```console
root@ns3:~ # pkg install gitlab-ce
root@ns3:~ # pkg install www/gitlab
```

## 5. Cara Konfigurasi Gitlab
Di bagian ini, kami akan menjelaskan cara mengonfigurasi Gitlab di FreeBSD. Karena Anda harus melakukan banyak konfigurasi, kami sarankan untuk mengikuti setiap langkah yang kami jelaskan agar Anda tidak melewatkan satu pun skrip.

### a. Buat Koneksi
Setelah proses instalasi gitlab-ce selesai, buka direktori instalasi gitlab dan edit berkas konfigurasinya, Anda ikuti petunjuk yang ada di bagian atas berkas. Pada berkas `gitlab.yml`, aktifkan skrip di bawah ini.

```console
root@ns3:~ # cd /usr/local/www/gitlab-ce/config
root@ns3:/usr/local/www/gitlab-ce/config # ee gitlab.yml
connect_src: "'self' http://192.168.5.2:* ws://192.168.5.2:* wss://192.168.5.2:*"
allowed_hosts: [192.168.5.2]
gitlab:
    host: 192.168.5.2
    port: 8080
email_from: datainchi@gmail.com
email_reply_to: datainchi@gmail.com
user: git
time_zone: 'UTC'
```

### b. Buat Koneksi Database
Dalam file database.yml, ubah skrip database sesuai dengan database PostgreSQL yang Anda buat di atas.

```console
root@ns3:~ # cd /usr/local/www/gitlab-ce/config
root@ns3:/usr/local/www/gitlab-ce/config # ee database.yml
production:
  main:
    adapter: postgresql
    encoding: unicode
    database: gitlab
    username: usergitlab
    password: "router123"
    host: localhost
```

### c. Buat global git settings
Kita akan menggunakan pengguna git untuk menginisialisasi Gitlab, menjalankan perintah berikut untuk beralih ke pengguna git dan memperbarui beberapa pengaturan git globalnya.

```console
root@ns3:~ # mkdir -p /usr/local/git/repositories
root@ns3:~ # mkdir -p /usr/home/git && mkdir -p /usr/home/git/gitlab
root@ns3:~ # chown -R git:git /usr/local/git/repositories
root@ns3:~ # chown -R git:git /usr/home/git/
```
```console
root@ns3:~ # su - git
$ git config --global core.autocrlf input
$ git config --global gc.auto 0
$ git config --global repack.writeBitmaps true
$ git config --global receive.advertisePushOptions true
$ git config --global core.fsync objects,derived-metadata,reference
$ exit
root@ns3:~ #
```

### d. Ubah ownership and permissions
Pada beberapa direktori dan file Gitlab, Anda harus mengubah hak kepemilikan file dan izin file.

```console
root@ns3:~ # chown -R git:git /usr/local/www/gitlab-ce/log
root@ns3:~ # chown -R git:git /usr/local/www/gitlab-ce/tmp
root@ns3:~ # chown -R git:git /usr/local/www/gitlab-ce/public
root@ns3:~ # chown -R git:git /usr/local/www/gitlab-ce/builds
root@ns3:~ # chown -R git:git /usr/local/www/gitlab-ce/shared
```
```console
root@ns3:~ # chmod -R 770 /usr/local/www/gitlab-ce/log
root@ns3:~ # chmod -R 770 /usr/local/www/gitlab-ce/tmp
root@ns3:~ # chmod -R 770 /usr/local/www/gitlab-ce/public
root@ns3:~ # chmod -R 770 /usr/local/www/gitlab-ce/builds
root@ns3:~ # chmod -R 770 /usr/local/www/gitlab-ce/shared
root@ns3:~ # chmod -R 770 /usr/local/www/gitlab-ce/config
```

### e. Install Librari rubygem
Untuk mencegah hang selama instalasi Gitlab, konfigurasikan rubygem untuk menggunakan pustaka yang terinstal.

```console
root@ns3:~ # cd /usr/local/www/gitlab-ce
root@ns3:/usr/local/www/gitlab-ce # bundle config build.gpgme "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.rugged "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.re2 "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.charlock_holmes "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.gitlab-glfm-markdown "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.nkf "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.rouge "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.wmi-lite "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.plist "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.net-ssh "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.lockbox "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.unf_ext "--use-system-libraries"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.prometheus-client-mmap "--use-system-libraries"
```
Jika masih ada "kesalahan", Anda dapat mencoba perintah di bawah ini.

```console
root@ns3:/usr/local/www/gitlab-ce # bundle config build.prometheus-client-mmap "--platform ruby"
root@ns3:/usr/local/www/gitlab-ce # bundle config build.gitlab-glfm-markdown "--platform ruby"
root@ns3:/usr/local/www/gitlab-ce # gem install prometheus-client-mmap --platform ruby
root@ns3:/usr/local/www/gitlab-ce # gem install gitlab-glfm-markdown --platform ruby
```

### f. Install gems untuk PostgreSQL
GitLab Shell adalah perangkat lunak akses SSH dan manajemen repositori yang dikembangkan khusus untuk GitLab. Pertama-tama kita harus menginstal Ruby Gems.

```console
root@ns3:/usr/local/www/gitlab-ce # bundle install --without development test
```
Jalankan perintah instalasi untuk gitlab-shell.

```console
root@ns3:/usr/local/www/gitlab-ce # bundle exec rake gitlab:shell:install REDIS_URL=unix:/var/run/redis/redis.sock RAILS_ENV=production SKIP_STORAGE_VALIDATION=true
```
### g. Buat srip rc.d
Agar Gitlab dapat berjalan secara otomatis di server FreeBSD, Anda harus membuat skrip rc.d. Ikuti perintah di bawah ini.

Aktifkan gitlab agar berjalan secara otomatis di FreeBSD. Di berkas rc.conf, ketik skrip di bawah ini.

```console
root@ns3:~ # ee /etc/rc.conf
gitlab_enable="YES"
gitlab_authBackend="http://192.168.5.2:8080"
gitlab_workhorse_tcp="NO"
gitlab_workhorse_addr="192.168.5.2:8181"
gitlab_mail_room_enable="NO"
gitlab_allow_conflicts="NO"
gitlab_wait="120"

gitlab_pages_enable="YES"
gitlab_pages_dir="/var/tmp/gitlab_pages"
gitlab_pages_user="gitlab-pages"
gitlab_pages_group="gitlab-pages"
gitlab_pages_logfile="/var/log/gitlab_pages.log"
gitlab_pages_args="-config=/usr/local/share/gitlab-pages/gitlab-pages.conf"
```

### h. Jalankan Gitlab
Langkah selanjutnya adalah kita mengatur Redmine agar terhubung ke Ruby.

```console
root@ns3:/usr/local/www/gitlab-ce # bundle exec rake generate_secret_token
root@ns3:/usr/local/www/gitlab-ce # bundle exec rake db:migrate RAILS_ENV="production"
```
Langkah terakhir adalah menjalankan gitlab-ce.

```console
root@ns3:/usr/local/www/gitlab-ce # service gitlab restart
root@ns3:/usr/local/www/gitlab-ce # ruby bin/rails server -e production
```
Setelah Anda selesai membaca artikel ini, server Anda kini memiliki GitLab yang berfungsi penuh yang dihosting di server Anda sendiri. Anda dapat mulai mengimpor atau membuat proyek baru dan berbagi akses ke proyek tersebut dengan anggota tim Anda.

Gitlab akan secara berkala menambahkan fitur baru dan memperbarui platform, oleh karena itu, tidak ada salahnya untuk sering mengunjungi halaman platform GitLab guna memeriksa beranda proyek untuk mendapatkan informasi terbaru tentang perbaikan atau pemberitahuan penting.
