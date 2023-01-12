+++
title = "Bagian 2: Instalasi Clojure"
date = "2022-12-25T16:46:31+07:00"

#
# description is optional
#
description = "Bagian 2: Instalasi Clojure dan Leiningen"

tags = ["clojure","tutorial",]
+++

Di [bagian 1](https://riz.maulana.me/blog/2022/12/bagian-1-mengenal-clojure/), saya sudah menjelaskan apa itu Clojure beserta fitur, alasan kenapa harus belajar, dan kekurangannya. Di bagian 2 ini, kita akan mulai membangun Clojure environment agar bisa mengikuti tutorial ini. Environment yang sudah dibangun nantinya juga bisa dipakai untuk membuat project yang lebih besar. Untuk membangun Clojure environment, saya sangat menyarankan menggunakan Linux dan PC/Laptop dengan memori minimal 4GB. Untuk pengguna Windows, saya sarankan untuk memakai WSL. Di tutorial ini, saya memakai Ubuntu 22.04. Langkah-langkahnya seharusnya sama, tinggal disesuaikan perintah-perintah sesuai dengan OS yang dipakai.

## Instal Java Development Kit

Karena Clojure berjalan di JVM, maka yang harus kita instal pertama adalah Java Development Kit (JDK) minimal versi 11. Periksa dulu di OS kalian sudah terinstal JDK atau belum dengan perintah:

```bash 
$ java -version
```

Jika perintah di atas tidak dikenali dan tidak menampilkan versi JVM, maka kita harus menginstal JDK dahulu. Di Ubuntu, saya memakai Open JDK versi 11 dan menginstalnya dengan perintah berikut:

```bash 
$ sudo apt install openjdk-11-jdk
```

Setelah instalasi JDK berhasil, cek lagi versi yang sudah terinstal dengan perintah `java -version` seperti sebelumnya. Berikut adalah versi JDK yang sudah terinstal di environment saya.

![Versi JDK](/img/tutorial-clojure/bab-2/versi-jdk.png)

## Instal Clojure

Setelah menginstal JDK, langkah selanjutnya adalah menginstal Clojure compiler. Ada dua cara untuk menginstal Clojure compiler: via **Leiningen** atau **Clojure CLI**. 

[Leiningen](https://leiningen.org/) adalah project tool populer yang sering digunakan untuk mengelola project Clojure. Di dalamnya sudah terdapat Clojure compiler dan bisa langsung digunakan untuk membuat project skala besar sekalipun. Leiningen sangat cocok untuk pemula karena mudah digunakan.

Clojure CLI adalah *cara resmi* untuk menginstal Clojure compiler. Ia tergolong cukup baru karena dirilis sekitar 2018. Selain itu, menurut saya, jika dibandingkan dengan Leiningen, Clojure CLI ini sedikit lebih ribet.

Di tutorial ini, kita akan fokus memakai Leiningen. Namun saya sarankan untuk instal keduanya (jangan khawatir karena tidak akan bentrok) karena di luar sana banyak tutorial yang memakai Clojure CLI. Alur kerja di Leiningen dan Clojure CLI juga cukup sama, sehingga tidak akan jadi masalah kalau suatu saat nanti harus ganti tool.

### Instal Leiningen
Berikut adalah langkah-langkah instalasi Leiningen:
* Buka halaman resmi Leiningen di https://leiningen.org
* Unduh `lein` script di bagian **Install**. Beberapa browser mungkin butuh klik kanan -> Save Page As.
* Copy `lein` script yang sudah diunduh ke salah satu direktori di `PATH`. Cara memeriksa `PATH` bisa dengan perintah `$ echo $PATH`
    * Contoh: di laptop saya, `lein` script saya taruh di `/usr/local/bin`  dengan perintah
    ```bash
    $ sudo cp Downloads/lein /usr/local/bin
    ```
* Set `lein` script menjadi executable 
    ```bash
    $ sudo chmod a+x /usr/local/bin/lein
    ```
* Jalankan lein di terminal untuk memulai instalasi compiler dan library
    ```bash
    $ lein
    ```
    
    ![Instalasi Lein](/img/tutorial-clojure/bab-2/lein-install.png)
    
* Setelah proses instalasi selesai, masuk ke REPL dengan perintah:
    ```bash 
    $ lein repl
    ```
    
* Apabila tampilan seperti screenshot di bawah, berarti instalasi Leiningen sudah berhasil. Silakan dicoba untuk mengetik kode Clojure seperti `(+ 1 2)` dan `(println "halo")` di REPL. Untuk keluar dari REPL, tekan `Ctrl+D`.

    ![Lein REPL](/img/tutorial-clojure/bab-2/lein-repl.png)
    
## Instalasi Clojure CLI
Panduan instalasi Clojure CLI yang lebih lengkap sebenarnya sudah tersedia di halaman resmi Clojure di https://clojure.org/guides/install_clojure. Di sini saya akan menunjukan contoh instalasi di laptop saya.
* Instal dependensi terlebih dahulu.
    ```bash
    $ sudo apt install curl bash rlwrap
    ```
* Unduh script instalasi. Di tutorial ini, saya memakai versi 1.11.
    ```bash
    $ curl -O https://download.clojure.org/install/linux-install-1.11.1.1182.sh
    ```
* Set script instalasi menjadi executable 
    ```bash
    $ chmod +x linux-install-1.11.1.1182.sh
    ```
* Mulai proses instalasi dengan perintah: 
    ```bash
    $ sudo ./linux-install-1.11.1.1182.sh
    ```
* Setelah proses instalasi selesai, kita akan diberikan 3 executable file di `/usr/local/bin/clj`, `/usr/local/bin/clojure`, dan `/usr/local/lib/clojure`. Sampai langkah ini, Clojure compiler masih belum sepenuhnya terinstal di environment kita dan harus diunduh terlebih dahulu. Jalankan perintah di bawah untuk membuka REPL dan mengunduh Clojure compiler (proses unduh hanya satu kali dan Clojure compiler akan disimpan)
    ```bash
    $ clojure
    ```
    
    ![CLI REPL](/img/tutorial-clojure/bab-2/cli-repl-1.png)
    
    > Perintah `clojure` maupun `clj` dapat digunakan untuk memulai REPL, namun sangat disarankan memakai `clj` karena terdapat beberapa fitur yang sangat membantu seperti *expression history* yang menyimpan ekspresi yang pernah dijalankan di REPL.
    
* Jika instalasi berhasil, maka akan muncul tampilan REPL seperti di bawah ini. Sama seperti sebelumnya, silakan dicoba untuk mengetik kode Clojure seperti `(+ 5 9 1)` dan `(println "halo dari clojure cli")` di REPL. Tekan `Ctrl+D` untuk keluar dari REPL. File instalasi dapat dihapus jika sudah berhasil.

    ![CLI REPL 2](/img/tutorial-clojure/bab-2/cli-repl-2.png)

## Penutup
Apabila tidak ada masalah saat instalasi, seharusnya kalian sekarang sudah punya Clojure compiler dan REPL yang bisa digunakan untuk menulis program secara interaktif. Kita masih membutuhkan IDE yang cocok untuk memudahkan kita menulis program Clojure. Di tulisan berikutnya, saya akan menjelaskan cara instalasi salah satu IDE yang sangat direkomendasikan untuk Clojure development, yaitu Emacs.
