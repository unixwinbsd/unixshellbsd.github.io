---
title: "Gitlab Runner Di FreeBSD - Proses Instalasi dan Cara Penggunaannya"
date: 2025-05-07T11:35:41+08:00
tags: ["Anonymous"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "GitLab Runner adalah proyek sumber terbuka yang digunakan untuk menjalankan pekerjaan Anda dan mengirimkan hasilnya kembali ke GitLab. Proyek ini digunakan bersama dengan GitLab CI/CID, layanan integrasi berkelanjutan sumber terbuka yang disertakan dengan GitLab yang
mengkoordinasikan pekerjaan"
summary: "GitLab Runner adalah proyek sumber terbuka yang digunakan untuk menjalankan pekerjaan Anda dan mengirimkan hasilnya kembali ke GitLab. Proyek ini digunakan bersama dengan GitLab CI/CID, layanan integrasi berkelanjutan sumber terbuka yang disertakan dengan GitLab yang
mengkoordinasikan pekerjaan"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---
Tampaknya Gitlab memiliki ambisi besar untuk menggeser Github, karena banyak sekali aplikasi yang dibuat untuk mengelola repositori Gitlab. Dengan aplikasi tersebut, sangat memudahkan pengguna Gitlab untuk terhubung dan berkomunikasi dengan repositorinya di Gitlab. Seolah pengguna gitlab dimanjakan dengan kemudahan aplikasi tersebut. Salah sau aplikasi yang sering digunakan oleh para pengmbang diantaranya adalah `gitlab-runner`.

GitLab Runner adalah proyek sumber terbuka yang digunakan untuk menjalankan pekerjaan Anda dan mengirimkan hasilnya kembali ke GitLab. Proyek ini digunakan bersama dengan GitLab CI/CID, layanan integrasi berkelanjutan sumber terbuka yang disertakan dengan GitLab yang
mengkoordinasikan pekerjaan. Dengan kata lain, ini adalah komponen penting dari alur kerja integrasi dan penerapan berkelanjutan GitLab.

Sebagai aplikasi yang berjalan pada mesin atau kontainer terpisah, tujuan utama GitLab Runner adalah untuk menjalankan pekerjaan dari konfigurasi GitLab CI/CD. Saat kita mengirim kode ke repositori GitLab, runner mengambil pekerjaan, menjalankan perintah yang ditentukan, dan melaporkan hasilnya kembali ke GitLab. Jadi, dengan menggunakan GitLab Runner, kita dapat mengotomatiskan proses pembuatan, pengujian, dan penerapan, menghemat waktu dan mengurangi risiko kesalahan manual.

Dalam tutorial yang kami tulis ini, kita akan melihat cara menyiapkan dan mengonfigurasi GitLab Runner di FreeBSD, serta berbagai fitur dan kemampuannya.

## 1. Spesifikasi Sistem
- OS: FreeBSD 14. 3
- Hostname: ns4
- IP Address: 192.168.5.71
- Gilab Runner Version: 18.0.0


## 2. Kode Runner Executors
Dalam menjalankan tugasnya, GitLab Runner mengimplementasikan berbagai eksekutor yang dapat digunakan untuk menjalankan build Anda di berbagai lingkungan.

GitLab Runner menyediakan eksekutor berikut:

| Executor       | Required configuration          | Where jobs are executed        | 
| ----------- | -----------   | ----------- |
| shell          |           | Local shell. Default executor.          |
| docker          | [runners.docker] and Docker Engine      | Docker container.          |
| docker-windows          | [runners.docker] and Docker Engine     | Windows Dockercontainer.          |
| docker-ssh          | [runners.docker], [runners.ssh], and Docker Engine  | Docker container, but with an SSH connection. The Docker container is executed on the local machine. This setting changes how commands are run inside the container. If you want to run docker commands on an external machine, change the parameterhostin the sectionrunners.docker.          |
| ssh          | [runners.ssh]  | SSH, remotely.          |
| parallels          | [runners.parallels] and [runners.ssh]  | Parallels VM, SSH connection.          |
| virtualbox          | [runners.virtualbox] and [runners.ssh]  | VirtualBox VM, SSH connection.          |
| docker+machine          | [runners.docker] and [runners.machine]  | Likedocker, but uses auto-scaled Docker machines.          |
| docker-ssh+machine          | [runners.docker] and [runners.machine]  | Likedocker-ssh, but uses auto-scaled Docker machines.          |
| kubernetes          | [runners.kubernetes]  | Kubernetes pods.          |



## 3. Instalasi Gitlab Runner
Pada FreeBSD Gitlab Runner telah tersedia di paket PKG dan sistem ports. Anda dapat langsung menginstalnya dengan salah satu paket tersebut. Kami sarankan gunakan paket PKG untuk mempermudah proses instalasi. Di bawah ini adalah perintah yang dapat anda jalankan untuk menginstal Gitlab Runner

```console
root@ns4:~ # pkg install devel/gitlab-runner
```
Perlu anda catat, selama proses instalasi berlangsung, sistem akan membuat user dan group untuk Gitlab Runner dengan nama `gitlab-runner:gitlab-runner`.

### 3.1. Mengaktifkan Gitlab Runner
Setelah proses instalasi selesai, anda dapat langsung mengaktifkan Gitlab Runner di file `/etc/rc.conf`. Pada file tersebut ketikkan skrip di bawah ini.

```console
gitlab_runner_enable="YES"
gitlab_runner_dir="/var/tmp/gitlab_runner"
gitlab_runner_user="gitlab-runner"
gitlab_runner_group="gitlab-runner"
gitlab_runner_syslogtag="gitlab-runner"
gitlab_runner_svcj_options="net_basic"
```

### 3.2. Menjalankan Gitlab Runner
Skrip di file `/etc/rc.conf` digunakan untuk mengaktifkan Gilab Runner agar dapat berjalan secara otomatis. Anda juga dapat menjalankan Gitlab Runner dengan menjalankan perintah berikut.

```console
root@ns4:~ #   service gitlab_runner restart
Stopping gitlab_runner.
Waiting for PIDS: 1156, 1156.
Starting gitlab_runner.
root@ns4:~ #
```

### 3.3. Daftarkan Gitlab Runner
Gitlab Runner yang telah anda aktifkan harus segera didaftarkan ke Gitlab agar fungsinya bisa anda gunakan. Pendaftaran runner akan membuat koneksi antara runner dan GitLab, sehingga keduanya dapat berkomunikasi dan menjalankan tugas.

Untuk membuat Runner, anda harus mendaftarkan Runner anda di situs Gitlab. Pada repositori kerja anda yang ada di Gitlab, klik menu `Settings  >> CI/CD`, setelah itu anda klik menu `Runners`. Pada menu `Project runners`, anda klik menu `create project runner`. Perhatikan gambar berikut ini.
<br><br/>
![create project runner gitlab](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/create%20project%20runner.jpg)
<br><br/>
![configuration runner](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/configuration%20runner.jpg)
<br><br/>
Setelah itu akan tampil kode token runner, kode tersebut harus anda daftarkan melalui menu shell FreeBSD.
<br><br/>
![register runner](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/register%20runner.jpg)
<br><br/>
Pada gambar ke-3, `Step 1` dan `Step 3` harus anda jalankan melalui menu Shell FreeBSD. Berikut ini perintah untuk mendaftarkan Gitlab Runner.

```console
root@ns4:~ # gitlab-runner register  --url https://gitlab.com  --token glrt-pJESaf7VlJfjyaryLccHN286MQpwOjE1djNkegp0OjMKdTpncXhwcBg.01.1j1mlp8bv
Runtime platform                                    arch=amd64 os=freebsd pid=1078 revision=18.0.0 version=18.0.0
Running in system-mode.
WARNING: You need to manually start builds processing:
WARNING: $ gitlab-runner run

Enter the GitLab instance URL (for example, https://gitlab.com/):
[https://gitlab.com]:
Verifying runner... is valid                        runner=pJESaf7Vl
Enter a name for the runner. This is stored only in the local config.toml file:
[ns4]:
Enter an executor: kubernetes, shell, ssh, docker, docker-windows, docker+machine, docker-autoscaler, instance, custom, parallels, virtualbox:
shell
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!

Configuration (with the authentication token) was saved in "/usr/local/etc/gitlab-runner/config.toml"
root@ns4:~ #
```

```console
root@ns4:~ # gitlab-runner run
```
Pada sistem FreeBSD, semua informasi Gitlab Runner yang telah anda buat di atas akan disimpan di file `/usr/local/etc/gitlab-runner/config.toml`.

Untuk melihat isi skirp dari file tersebut, jalankan perintah `cat`.

```console
root@ns4:~ # cat /usr/local/etc/gitlab-runner/config.toml
concurrent = 1
check_interval = 0
connection_max_age = "15m0s"
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "ns4"
  url = "https://gitlab.com"
  id = 47669129
  token = "glrt-pJESaf7VlJfjyaryLccHN286MQpwOjE1djNkegp0OjMKdTpncXhwcBg.01.1j1mlp8bv"
  token_obtained_at = 2025-05-31T08:45:47Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "shell"
  [runners.cache]
    MaxUploadedArchiveSize = 0
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
root@ns4:~ #
```


### 3.4. Menampilkan Daftar Gitlab Runner
Pada Gitlab anda dapat membuat banyak Runner, kami sarankan buat Runner sesuai keperluan saja. Untuk melihat daftar Runner yang telah anda buat, jalankan perintah berikut.

```console
root@ns4:~ # service gitlab_runner list
Runtime platform                                    arch=amd64 os=freebsd pid=1113 revision=18.0.0 version=18.0.0
Listing configured runners                          ConfigFile=/var/tmp/gitlab_runner/.gitlab-runner/config.toml
runner1234                                          Executor=shell Token=glrt-NgUu45FU3jLLOE6YbT6Hd286MQpwOjE1djNkegp0OjMKdTpncXhwcBg.01.1j0dbf347 URL=https://gitlab.com
```

### 3.5. Opsi (flags) Gitlab Runner
Perintah `gitlab-runner` memiliki banyak sekali opsi, namun opsi yang kami jelaskan di atas salah satu perintah yang sangat penting dan harus dijalankan ketika anda akan menggunakan Gitlab Runner. Berikut ini adalah daftar opsi perintah `gitlab-runner` yang dapat anda coba.

| Option       | Description          | 
| ----------- | -----------   | 
| list          | List all configured runners          | 
| run          | run multi runner service      | 
| register          | register a new runner     | 
| reset-token          | reset a runner's token          | 
| install          | install service          | 
| uninstall          | uninstall service          | 
| start          | start service          | 
| Stop          | stop service          | 
| restart          | restart service          | 
| status          | get status of a service          | 
| run-single          | start single runner          | 
| unregister          | unregister specific runner          | 
| verify          | verify all registered runners          | 
| wrapper          | start multi runner service wrapped with gRPC manager server          | 
| fleeting          | manage fleeting plugins          | 
| artifacts-downloader          | download and extract build artifacts (internal)          | 
| artifacts-uploader          | create and upload build artifacts (internal)          | 
| cache-archiver          | create and upload cache artifacts (internal)          | 
| cache-extractor          | download and extract cache artifacts (internal)          | 
| cache-init          | changed permissions for cache paths (internal)          | 
| health-check          | check health for a specific address          | 
| proxy-exec          | execute internal commands (internal)          | 
| read-logs          | reads job logs from a file, used by kubernetes executor (internal)          | 
| help, h          | Shows a list of commands or help for one command          | 
|                                                                               |
| --cpuprofile value          | write cpu profile to file [$CPU_PROFILE]          | 
| --debug          | debug mode [$RUNNER_DEBUG]          | 
| --log-format value          | Choose log format (options: runner, text, json) [$LOG_FORMAT]          | 
| --log-level value, -l value          | Log level (options: debug, info, warn, error, fatal, panic) [$LOG_LEVEL]          | 
| --help, -h          | show help          | 
| --version, -v          | print the version          | 



Singkatnya, gitlab-runner adalah alat penting untuk mengelola eksekusi pekerjaan CI/CD di GitLab. Alat ini menyederhanakan pendaftaran, konfigurasi, penyebaran, dan pengelolaan GitLab Runner, sehingga Anda dapat mengotomatiskan dan mengatur proses pengembangan dan penyebaran perangkat lunak secara efisien.

Dengan menggunakan contoh yang diberikan, Anda dapat mendaftarkan, mengelola, dan memverifikasi runner secara efisien, sehingga proses pengembangan hingga penerapan berjalan lancar. Baik saat menyiapkan lingkungan baru, memelihara sistem aktif, atau memecahkan masalah konektivitas, alat ini membekali Anda dengan kemampuan yang dibutuhkan untuk beradaptasi dengan tuntutan proyek yang terus berkembang.
