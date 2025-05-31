---
title: "Membuat Aplikasi Web dengan Google Go Lang Part 1"
date: 2025-05-23T07:57:45+08:00
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

Go atau bahasa Go adalah bahasa pemrograman populer yang banyak digunakan dalam industri untuk membuat berbagai aplikasi, termasuk server web, blog, alat jaringan, dan utilitas sistem. Go dikenal karena kesederhanaannya, efisiensinya, dan dukungan konkurensinya, sehingga cocok untuk membangun sistem yang dapat diskalakan dan berkinerja tinggi.


Go digunakan oleh banyak perusahaan dan organisasi, termasuk:
- **Google:** Google menggunakan Go di sejumlah sistem internalnya, termasuk platform orkestrasi kontainer Kubernetes dan mesin pencari Google Search.
- **Netflix:** Netflix menggunakan Go di sejumlah sistemnya, termasuk jalur data dan mesin rekomendasinya.
- **Dropbox:** Dropbox menggunakan Go untuk sejumlah layanannya, termasuk mesin sinkronisasi berkas dan infrastruktur servernya.
- **Uber:** Uber menggunakan Go di sejumlah sistemnya, termasuk penyimpanan data terdistribusi dan mesin perutean.

Salah satu kekuatan terbesar Go terletak pada kesesuaiannya untuk mengembangkan aplikasi web. Bahasa pemrograman Go milik Google menawarkan kinerja yang hebat, mudah digunakan, dan memiliki banyak alat penting yang Anda butuhkan untuk membangun dan menggunakan layanan web yang dapat diskalakan dalam pustaka standarnya.

Tutorial ini akan menjelaskan dan memandu Anda untuk membuat contoh praktis membangun aplikasi web dengan Go dan menggunakannya di jaringan internet sehingga dapat dibaca oleh banyak orang. Panduan ini akan membahas dasar-dasar penggunaan server HTTP bawaan Go dan bahasa templating, serta cara berinteraksi dengan API eksternal.


## 1. Spesifikasi Sistem
- OS: FreeBSD 13.2
- Hostname/Domain: ns1@unixexplore.com
- IP Address: 192.168.5.2
- go version: go1.20.7 freebsd/amd64
- Port GoLang Web: 8999


## 2. Create Simple Web Applications

Langkah pertama untuk memulai Go adalah menginstalnya di komputer server kita. Untuk proses instalasi Go, Anda dapat membaca artikel kami sebelumnya: "How to Install GOLANG GO Language on FreeBSD". Anda dapat mempraktikkan semua konten artikel ini pada mesin server Linux seperti Ubuntu, Debian dan lainnya, serta dapat juga diterapkan pada sistem operasi Windows dan MacOS.

Sebagai materi dasar atau materi pembuka, kita mulai dengan membuat aplikasi web sederhana yang hanya menampilkan teks di browser web. Untuk memulai, kita akan membuat folder atau direktori kerja, berikut langkah-langkahnya.

```console
root@ns1:~ # mkdir -p /var/GoogleBlog
root@ns1:~ # cd /var/GoogleBlog
```
Penjelasan dari skrip di atas adalah membuat direktori kerja dengan nama folder GoogleBlog yang kita tempatkan di folder `/var/GoogleBlog`. Setelah kita berhasil membuat direktori kerja, langkah selanjutnya adalah menuliskan kode program dalam bahasa GO.

Program Go disusun menjadi paket-paket. Paket adalah kumpulan file sumber dalam direktori yang sama yang dikompilasi bersama-sama. Fungsi, tipe, variabel, dan konstanta yang didefinisikan dalam satu file sumber dapat dilihat oleh semua file sumber lain dalam paket yang sama.

Repositori berisi satu atau beberapa modul. Modul adalah kumpulan paket-paket Go terkait yang dirilis bersama-sama. Repositori Go biasanya hanya berisi satu modul, yang terletak di akar repositori. File bernama go.mod mendeklarasikan awalan jalur modul:import untuk semua paket dalam modul. Modul berisi paket-paket dalam direktori yang berisi file go.mod serta subdirektori dari direktori tersebut, hingga subdirektori berikutnya yang berisi file-file go.mod lainnya (jika ada).

Untuk mengkompilasi dan menjalankan program sederhana, pertama-tama pilih jalur modul (kita akan menggunakan direktori kerja di atas dan membuat file `go.mod` untuk mendeklarasikan modul.

```console
root@ns1:~ # cd /var/GoogleBlog
root@ns1:/var/GoogleBlog # go mod init GoogleBlog
go: creating new go.mod: module GoogleBlog
root@ns1:/var/GoogleBlog #
```
Skrip di atas akan membuat file `go.mod` yang akan kita gunakan untuk mengkompilasi dan menjalankan program Go. Setelah file kompilasi `go.mod` berhasil dibuat, langkah selanjutnya adalah membuat file utama Go yang kita sebut `"main.go`" dan memasukkan kode skrip di bawah ini ke dalam file `"main.go"`.

```html
root@ns1:/var/GoogleBlog # ee main.go

package main

import (
	"net/http"
	"os"
)

func indexHandler(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("<h1>Good Morning, FreeBSD!</h1>"))
}

func main() {
	port := os.Getenv("PORT")
	if port == "" {
		port = "8999"
	}

	mux := http.NewServeMux()

	mux.HandleFunc("/", indexHandler)
	http.ListenAndServe("192.168.5.2:"+port, mux)
}
```
Lalu kita jalankan file `"main.go"` dengan mengetikkan skrip di bawah ini.

```console
root@ns1:/var/GoogleBlog # go run main.go
```
Kita dapat melihat hasilnya dengan membuka web browser Mozilla Firefox atau Google Chrome, pada menu address bar ketik `"http://192.168.5.2:8999/"` dan hasilnya akan tampak seperti gambar di bawah ini.


![contoh aplikasi web dengan GO](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/Contoh%20Aplikasi%20dengan%20Go%20Golang.jpg)


Contoh skrip lain yang bisa Anda praktikkan, hapus semua skrip di file "main.go" dan ganti dengan skrip di bawah ini.

```html
package main
import (
    "fmt"
    "net/http"
)
type msg string
func (m msg) ServeHTTP(resp http.ResponseWriter, req *http.Request) {
   fmt.Fprint(resp, m) 
}
func main() {
    msgHandler := msg("Hello from Web Server in Go")
    fmt.Println("Server is listening...")
    http.ListenAndServe("192.168.5.2:8999", msgHandler)
}
```
Jalankan aplikasi Go.

```console
root@ns1:/var/GoogleBlog # go run main.go
```
Buka Google Chrome dan ketik `"http://192.168.5.2:8999/"` di menu bilah alamat dan Anda dapat melihat hasilnya di Google Chrome.


## 3. Menentukan Rute dan Fungsi Penangan dalam Go

Ada banyak cara untuk melakukan routing jalur HTTP di Go, termasuk pustaka standar http.ServeMux, tetapi pustaka tersebut hanya mendukung pencocokan awalan dasar. Selain itu, ada juga banyak pustaka router pihak ketiga. Di Go, routing dapat dilakukan dengan beberapa cara, termasuk:
- Dengan memanfaatkan fungsi http.HandleFunc().
- Mengimplementasikan antarmuka http.Handler dalam suatu struktur, untuk kemudian digunakan dalam fungsi http.Handle().
- Membuat multiplexer Anda sendiri menggunakan struktur http.ServeMux.
- Dan lain-lain.

Kita dapat menentukan jalur untuk aplikasi dan fungsi pengontrol yang sesuai. Router di Go merupakan kombinasi metode HTTP (seperti GET, PUT, atau POST) dan pola URL yang harus ditanggapi oleh aplikasi Go. Untuk menentukan perutean, kita akan menggunakan fungsi http.HandleFunc yang mengambil pola dan fungsi pengendali sebagai argumen. Misalnya.

```html
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
  // Add handler code here
})
```
Skrip di atas akan menentukan jalur yang merespons permintaan GET di URL "/". Fungsi pengontrol akan menerima dua argumen, antarmuka ResponseWriter, yang digunakan untuk menulis respons ke klien, dan struktur Permintaan, yang berisi informasi tentang permintaan yang dibuat oleh klien.

Selanjutnya, kita harus menulis fungsi pengontrol untuk jalur aplikasi yang akan kita buat. Fungsi-fungsi ini akan bertugas melakukan tindakan yang sesuai untuk setiap jalur dan mengembalikan respons ke klien. Misalnya, jika kita membuat server yang menampilkan daftar pengguna, Anda mungkin memiliki fungsi pengontrol yang meminta daftar pengguna ke database, lalu merender templat HTML dengan daftar pengguna yang telah disimpan dalam database.

Untuk merender templat HTML, pertama-tama Anda harus mengurai berkas templat menggunakan fungsi template.ParseFiles. Seperti contoh di bawah ini.

```html
tmpl, err := template.ParseFiles("template.html")
if err != nil {
  http.Error(w, err.Error(), http.StatusInternalServerError)
  return
}
```
Template HTML adalah berkas yang berisi tata letak dan konten halaman aplikasi. Kita dapat menyertakan placeholder untuk data dinamis yang akan diteruskan oleh fungsi kontroler. Misalnya, berikut ini adalah template HTML sederhana yang menampilkan daftar pengguna. Ketik skrip di bawah ini di file `/var/GoogleBlog/index.html`.

```html
root@ns1:~ # cd /var/GoogleBlog
root@ns1:/var/GoogleBlog # ee index.html

<html>
<head>
        <title>{{.Title}}</title>
</head>
<body>
        <h1>{{.Title}}</h1>
        <ul>
                {{range .Users}}
                        <li>{{.Name}} ({{.Age}} years old)</li>
                {{end}}
        </ul>
</body>
<html>
<head>
        <title>{{.Title}}</title>
</head>
<body>
        <h1>{{.Title}}</h1>
        <ul>
                {{range .Users}}
                        <li>{{.Name}} ({{.Age}} years old)</li>
                {{end}}
        </ul>
        <form action="/add" method="post">
                <label>Name: <input type="text" name="name"></label>
                <br>
                <label>Age: <input type="number" name="age"></label>
                <br>
                <input type="submit" value="Add user">
        </form>
</body>
</html>
```
Setelah itu, kita buat file utama Go yang kita beri nama `"/var/GoogleBlog./main.go"` dan masukkan skrip di bawah ini ke dalam file `"main.go"`.

```html
root@ns1:/var/GoogleBlog # ee main.go

package main

import (
	"html/template"
	"net/http"
	"strconv"
)

type User struct {
	Name string
	Age  int
}

var users []User

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		// Parse the HTML template
		tmpl, err := template.ParseFiles("index.html")
		if err != nil {
			http.Error(w, err.Error(), http.StatusInternalServerError)
			return
		}

		// Define the data for the template
		data := struct {
			Title string
			Users []User
		}{
			Title: "Users",
			Users: users,
		}

		// Render the template with the data
		err = tmpl.Execute(w, data)
		if err != nil {
			http.Error(w, err.Error(), http.StatusInternalServerError)
			return
		}
	})

	http.HandleFunc("/add", func(w http.ResponseWriter, r *http.Request) {
		if r.Method == http.MethodPost {
			// Parse the form values
			name := r.FormValue("name")
			ageStr := r.FormValue("age")
			age, err := strconv.Atoi(ageStr)
			if err != nil {
				http.Error(w, "Invalid age", http.StatusBadRequest)
				return
			}

			// Add the user to the list of users
			users = append(users, User{Name: name, Age: age})

			// Redirect the user back to the homepage
			http.Redirect(w, r, "/", http.StatusSeeOther)
		}
	})

	fs := http.FileServer(http.Dir("static"))
	http.Handle("/static/", http.StripPrefix("/static/", fs))

	http.ListenAndServe("192.168.5.2:8999", nil)
}
```
Langkah selanjutnya adalah, kita menjalankan server Go, dengan mengetik perintah di bawah ini.

```console
root@ns1:/var/GoogleBlog # go run main.go
```
Kita dapat melihat hasilnya dengan membuka web browser Google Chrome atau Yandex, pada menu address bar ketik `"http://192.168.5.2:8999/"`, maka akan muncul tampilan seperti gambar di bawah ini.


![google go lang dengan skrip html](https://gitea.com/UnixBSDShell/Web-Static-With-Ruby-Jekyll-Site-NetBSD/raw/branch/main/images/google%20go%20lang%20dengan%20skrip%20html.jpg)


Dalam tutorial ini, kita akan belajar cara membuat aplikasi web di Go dari awal. Kita juga akan belajar cara mengimpor paket yang diperlukan, menentukan jalur dan fungsi pengontrol, membuat templat HTML, dan memulai server HTTP. Artikel ini sudah selesai untuk saat ini, kita akan melanjutkan ke artikel berikutnya dengan materi yang cukup sulit dan skrip yang menantang.
