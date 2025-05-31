---
title: "Membuat Aplikasi Web dengan Google Go Lang Part 2"
date: 2025-05-23T10:15:15+08:00
tags: ["WebHTTPS"]
categories: ["FreeBSD"]
author: "Iwan Setiawan"
description: "Tutorial ini akan menjelaskan dan memandu Anda untuk membuat contoh praktis membangun aplikasi web dengan Go dan menggunakannya di jaringan internet sehingga dapat dibaca oleh banyak orang"
summary: "Tutorial ini akan menjelaskan dan memandu Anda untuk membuat contoh praktis membangun aplikasi web dengan Go dan menggunakannya di jaringan internet sehingga dapat dibaca oleh banyak orang"
editPost:
    URL: "https://github.com/unixwinbsd/unixwinbsd.github.io"
    Text: "GitHub repository"
showToc: true
disableAnchoredHeadings: false
---

Membuat aplikasi Restful merupakan salah satu cara populer untuk membangun blog web karena mengikuti arsitektur yang sederhana dan dapat diskalakan. Dengan CRUD, pengguna dapat dengan mudah memproses basis data MySQL. CRUD yang merupakan singkatan dari Create, Read, Update, dan Delete merupakan utilitas umum dalam pengembangan perangkat lunak. Kemampuan untuk melakukan operasi CRUD merupakan hal mendasar bagi pengembangan aplikasi apa pun. Dalam artikel ini, kita akan mempelajari cara membangun aplikasi CRUD sederhana menggunakan Go Lang dan server MySQL sebagai basis datanya.

Go Lang merupakan bahasa pemrograman populer yang dikenal karena efisiensinya, kesederhanaannya, dan stabilitasnya, sedangkan MySQL merupakan sistem manajemen basis data relasional (RDBMS) yang dikembangkan oleh Oracle berdasarkan bahasa kueri terstruktur (SQL).

MySQL merupakan salah satu teknologi basis data paling populer dalam ekosistem big data modern, dan kini digunakan secara luas dan efektif, apa pun industrinya, jelas bahwa siapa pun yang terlibat dengan data perusahaan atau TI setidaknya harus memiliki pemahaman dasar tentang MySQL.

Dengan MySQL, bahkan mereka yang baru mempelajari sistem basis data relasional dapat langsung membangun sistem penyimpanan data yang mudah, cepat, dan aman. Data dalam MySQL disusun menurut model relasional. Dalam model ini, tabel terdiri dari baris dan kolom, dan hubungan antara elemen data semuanya mengikuti struktur logika yang ketat. Sering kali orang yang baru mempelajari Golang tidak tahu cara menyimpan data di MySQL karena tidak banyak sumber daya daring. Artikel ini akan membantu Anda memahami konsep Golang dengan MySQL.


## 1. Spesifikasi Sistem yang Digunakan
- OS: FreeBSD 14.
- IP Address: 192.168.5.2.
- Go version: go-1.19.
- MySql Server.
- Hostname: ns7.

## 2. Buat proyek Golang
Langkah pertama adalah membuat direktori kerja untuk menyiapkan lingkungan pengembangan dan memulai proyek Golang. Ikuti langkah-langkah di bawah ini untuk menyiapkan proyek Golang baru.

```console
root@ns7:~ # cd /var
root@ns7:/var # mkdir -p FreeBSD-Golang-MySQL
root@ns7:/var # cd FreeBSD-Golang-MySQL
root@ns7:/var/FreeBSD-Golang-MySQL #
```
Setelah Anda selesai membuat direktori bahasa Go, buat file baru bernama `"main.go"`.

```console
root@ns7:/var/FreeBSD-Golang-MySQL # touch main.go
root@ns7:/var/FreeBSD-Golang-MySQL # chmod +x main.go
```
Pada file `"/root/var/FreeBSD-Golang-MySQL/main.go"`, Anda masukkan skrip di bawah ini.

```html
package main

import (
	"database/sql"
	"html/template"
	"log"
	"net/http"

	_ "github.com/go-sql-driver/mysql"
)

// Employee Struct
type Employee struct {
	ID   int
	Name string
	City string
}

// Open Connection with MySQL Driver
func dbConnect() (db *sql.DB) {
	dbDriver := "mysql"
	dbUser := "jhondoe"
	dbPass := "router"
	dbName := "goblog"
	db, err := sql.Open(dbDriver, dbUser+":"+dbPass+"@/"+dbName)
	if err != nil {
		panic(err.Error())
	}
	return db
}

// Read All Templates on folder template
var tmpl = template.Must(template.ParseGlob("template/*"))

// Index Page
func Index(w http.ResponseWriter, r *http.Request) {
	db := dbConnect()
	rows, err := db.Query("SELECT * FROM Employee ORDER BY id DESC")
	if err != nil {
		panic(err.Error())
	}
	emp := Employee{}
	res := []Employee{}
	for rows.Next() {
		var id int
		var name, city string
		err = rows.Scan(&id, &name, &city)
		if err != nil {
			panic(err.Error())
		}
		emp.ID = id
		emp.Name = name
		emp.City = city
		res = append(res, emp)
	}
	tmpl.ExecuteTemplate(w, "Index", res)
	defer db.Close()
}

// Show Single Item
func Show(w http.ResponseWriter, r *http.Request) {
	db := dbConnect()
	nID := r.URL.Query().Get("id")
	rows, err := db.Query("SELECT * FROM Employee WHERE id = ?", nID)
	if err != nil {
		panic(err.Error())
	}
	emp := Employee{}
	for rows.Next() {
		var id int
		var name, city string
		err = rows.Scan(&id, &name, &city)
		if err != nil {
			panic(err.Error())
		}
		emp.ID = id
		emp.Name = name
		emp.City = city
	}
	tmpl.ExecuteTemplate(w, "Show", emp)
	defer db.Close()
}

// Show New Page
func New(w http.ResponseWriter, r *http.Request) {
	tmpl.ExecuteTemplate(w, "New", nil)
}

// Edit Item
func Edit(w http.ResponseWriter, r *http.Request) {
	db := dbConnect()
	nID := r.URL.Query().Get("id")
	rows, err := db.Query("SELECT * FROM Employee WHERE id = ?", nID)
	if err != nil {
		panic(err.Error())
	}
	emp := Employee{}
	for rows.Next() {
		var id int
		var name, city string
		err = rows.Scan(&id, &name, &city)
		if err != nil {
			panic(err.Error())
		}
		emp.ID = id
		emp.Name = name
		emp.City = city
	}
	tmpl.ExecuteTemplate(w, "Edit", emp)
	defer db.Close()
}

// Insert Item
func Insert(w http.ResponseWriter, r *http.Request) {
	db := dbConnect()
	if r.Method == "POST" {
		name := r.FormValue("name")
		city := r.FormValue("city")
		insert, err := db.Prepare("INSERT INTO Employee (name, city) VALUES(?, ?)")
		if err != nil {
			panic(err.Error())
		}
		insert.Exec(name, city)
		log.Println("INSERT: Name: " + name + " | City: " + city)
	}
	defer db.Close()
	http.Redirect(w, r, "/", 301)
}

// Update Item
func Update(w http.ResponseWriter, r *http.Request) {
	db := dbConnect()
	if r.Method == "POST" {
		name := r.FormValue("name")
		city := r.FormValue("city")
		id := r.FormValue("uid")
		insert, err := db.Prepare("UPDATE Employee SET name = ?, city = ? WHERE id = ?")
		if err != nil {
			panic(err.Error())
		}
		insert.Exec(name, city, id)
		log.Println("UPDATE: Name: " + name + " | City: " + city)
	}
	defer db.Close()
	http.Redirect(w, r, "/", 301)
}

// Delete Item
func Delete(w http.ResponseWriter, r *http.Request) {
	db := dbConnect()
	emp := r.URL.Query().Get("id")
	delete, err := db.Prepare("DELETE FROM Employee WHERE id = ?")
	if err != nil {
		panic(err.Error())
	}
	delete.Exec(emp)
	log.Println("DELETE")
	defer db.Close()
	http.Redirect(w, r, "/", 301)
}

func main() {
	log.Println("Server started on: http://192.168.5.2:4000")
	// Routes
	http.HandleFunc("/", Index)
	http.HandleFunc("/show", Show)
	http.HandleFunc("/new", New)
	http.HandleFunc("/edit", Edit)
	http.HandleFunc("/insert", Insert)
	http.HandleFunc("/update", Update)
	http.HandleFunc("/delete", Delete)
	// Start server on port 4000
	http.ListenAndServe("192.168.5.2:4000", nil)
}
```
Perhatikan skrip `http://192.168.5.2:4000`, skrip tersebut adalah IP dan Port yang akan digunakan Go Lang untuk mengakses peramban Web. Gunakan perintah git untuk menginisialisasi proyek Go Lang Anda.

```console
root@ns7:/var/FreeBSD-Golang-MySQL # go mod init github.com/iwanse1977/FreeBSD-Golang-MySQL
root@ns7:/var/FreeBSD-Golang-MySQL # go mod tidy
root@ns7:/var/FreeBSD-Golang-MySQL # go get github.com/go-sql-driver/mysql
```
Dalam proyek kerja Anda, buat folder `"template"`, dan buat file `Edit.tmpl, Footer.tmpl, Header.tmpl, Index.tmpl, Menu.tmpl, New.tmpl, Show.tmpl`.

```console
root@ns7:/var/FreeBSD-Golang-MySQL # mkdir template
root@ns7:/var/FreeBSD-Golang-MySQL # cd template
root@ns7:/var/FreeBSD-Golang-MySQL/template # touch Edit.tmpl Footer.tmpl Header.tmpl Index.tmpl Menu.tmpl New.tmpl Show.tmpl
```
Download the script code of the "*.tmpl" file


## 3. Membuat Database MySQL
Proyek Go lang Anda hampir selesai, tinggal satu langkah lagi. Sekarang Anda membuat database MySQL sebagai backend dari Go lang. Silakan masuk ke database server MySQL, dengan kata sandi root.

```console
root@ns7:/var/FreeBSD-Golang-MySQL # mysql -u root -p
Enter password: "Enter your MySQL password"
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 83
Server version: 8.0.35 Source distribution

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

root@localhost [(none)]>
```
Buat database `"goblog"` dan tabel `"Employee"`.

```console
root@localhost [(none)]> create database goblog;
root@localhost [(none)]> use goblog;
Database changed
root@localhost [goblog]> CREATE TABLE IF NOT EXISTS `Employee` (
    ->   `id` int unsigned NOT NULL AUTO_INCREMENT,
    ->   `Name` text,
    ->   `city` text,
    ->   PRIMARY KEY (`id`)
    -> ) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb4;
Query OK, 0 rows affected, 1 warning (0.02 sec)

root@localhost [goblog]>
```
Buat pengguna MySQL, sehingga database MySQL dapat dihubungkan ke Go lang.

```console
root@localhost [(none)]> CREATE USER 'jhondoe'@'localhost' IDENTIFIED BY 'router';
root@localhost [(none)]> GRANT ALL PRIVILEGES ON * . * TO 'jhondoe'@'localhost';
root@localhost [(none)]> FLUSH PRIVILEGES;
```

## 4. Jalankan aplikasi
Sekarang setelah kita mengimplementasikan semua operasi MySQL dan Go lang, saatnya menguji aplikasi kita. Apakah berhasil atau tidak.

Buka peramban web Google Chrome, dan jalankan perintah "http://192.168.5.2:4000". Jika semua konfigurasi di atas sudah benar, Google Chrome akan menampilkan gambar berikut.

```console
root@ns7:~ # cd /var/FreeBSD-Golang-MySQL
root@ns7:/var/FreeBSD-Golang-MySQL # go run main.go
```

![contoh penggunaan crud mysql server](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/Contoh%20penggunaan%20Crud%20MysQL.jpg)


Dalam artikel ini, kita dapat mempelajari cara membuat aplikasi CRUD sederhana menggunakan GoLang dan MySQL pada server FreeBSD. Anda dapat mengembangkan aplikasi berikut dengan tampilan menu yang menarik. Ikuti terus artikel terbaru kami tentang Go Lang.
