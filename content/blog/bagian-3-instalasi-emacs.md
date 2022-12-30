+++
title = "Bagian 3: Instalasi dan Cara Pakai Emacs"
date = "2022-12-30T12:11:33+07:00"

#
# description is optional
#
description = "Bagian 3: Instalasi Emacs/Spacemacs dan cara pemakaiannya untuk interactive development."

tags = ["clojure","tutorial",]
+++

Setelah berhasil menginstal Clojure, kita masih memerlukan IDE yang cocok untuk menulis program Clojure. Sintaksis Lisp di Clojure sangat merepotkan apabila kita menulis di editor teks biasa karena tidak ada fitur *syntax highlighting*, *parenthesis matching*, *auto-indentation*, dsb. IDE yang direkomendasikan untuk Clojure development adalah Emacs, karena ia sudah memiliki berbagai fitur yang dibutuhkan. Selain itu, Emacs sendiri dibuat dengan salah satu dialek Lisp, yaitu Emacs Lisp atau Elisp. Di tutorial ini, saya tidak akan memakai Emacs versi *vanilla*, melainkan [**Spacemacs**](https://www.spacemacs.org/). Spacemacs adalah *configuration kit* yang berisi berbagai macam setting yang bermanfaat untuk development, sehingga kita tidak perlu lagi mengkonfigurasi Emacs dari awal. Spacemacs juga memiliki Vim mode, sehingga bagi kalian yang sudah terbiasa dengan Vim, Spacemacs seharusnya tidak terlalu asing.

Di tulisan ini saya akan menjabarkan langkah-langkah instalasi Emacs dan Spacemacs beserta contoh cara pakainya untuk Clojure development. Jujur saja Emacs memang cukup ribet digunakan, khususnya bagi pemula. Pemakaian Emacs sangat mengandalkan kibor, sehingga kita harus hapal *key bindings* yang diperlukan. Jadi kalau setelah selesai bagian ini kalian merasa kesusahan saya sangat paham. Kalau ingin memakai IDE lain, saya akan memberikan IDE alternatif yang cocok untuk menulis program Clojure. 

Saya ingatkan lagi, proses instalasi di bawah ini saya lakukan di Ubuntu 22.04. Jika di *environment* kalian belum ada Clojure compiler, silakan instal dulu seperti yang saya jelaskan di [bagian 2](https://riz.maulana.me/blog/2022/12/bagian-2-instalasi-clojure/).

> **Penting**: Bagi pemula atau yang belum pernah mencoba Emacs, sebelum memutuskan untuk instalasi, silakan baca bagian 3 ini secara sekilas. Lihat apakah kalian tidak ada masalah atau cocok dengan alur kerja seperti di bawah. Kalau tidak cocok, silakan pakai IDE/editor teks pilihan dan pastikan instal plugin untuk Clojure development. Emacs hanya salah satu opsi yang direkomendasikan, bukan keharusan.
>
> **Bagi yang sudah terbisa dengan Emacs**: Jika kalian sudah punya setup Emacs tersendiri, silakan disesuaikan untuk Clojure development.

## Instalasi Emacs (dan Spacemacs)

1. Langkah pertama adalah instalasi Emacs di PC/Laptop kita kalau belum ada.
    ```bash
    $ sudo apt install emacs ripgrep fd-find
    ```

2. Unduh konfigurasi Spacemacs dan tempatkan di direktori `~/.emacs.d`. Konfigurasi ini akan **menggantikan konfigurasi *existing***. Silakan backup konfigurasi *existing* terlebih dahulu jika ingin mencoba Spacemacs. Setelah ini saya akan menyebut Emacs sebagai Spacemacs.
    ```bash
    $ git clone https://github.com/syl20bnr/spacemacs ~/.emacs.d`
     ```

3. Buka Spacemacs GUI (di menu namanya mungkin masih tetap Emacs) dan di bagian bawah akan muncul **Dotfile wizard installer**.  Kita akan diminta memilih editing style (Emacs, Vim, atau *hybrid*) dan distribusi Spacemacs (*standard* atau *minimalist*). Untuk kedua pilihan tersebut, saya pilih Vim dan *standard distribution*. 

    ![1-edit](/img/tutorial-clojure/bab-3/1f-edit.png)


    ![2-distro](/img/tutorial-clojure/bab-3/2f-distro.png)

    Setelah itu, Spacemacs akan mulai mengunduh *packages* yang dibutuhkan. Tunggu proses unduh *packages* dan instalasinya sampai selesai.

    ![3-instal](/img/tutorial-clojure/bab-3/3f-instal.png)

4. Setelah proses instalasi *packages* selesai, kita akan dihadapkan dengan tampilan yang berisi peringatan dan log dari proses instalasi, seperti pada gambar di bawah. Untuk saat ini tutup Spacemacs lalu buka lagi. 

    ![4-post-instal](/img/tutorial-clojure/bab-3/4f-post-instal.png)

5. Apabila ada pesan *warning*, seperti *font* tidak ditemukan, kita bisa abaikan pesan *warning* tersebut. Namun, jika ada pesan error terutama karena instalasi *package*, coba *restart* Spacemacs dengan cara tutup dan buka Spacemacs seperti sebelumnya. Tiap *restart*, Spacemacs akan mencoba menginstal *package* yang masih tertinggal. Kadang koneksi ke server ELPA/MELPA tempat mengunduh package tidak stabil dan ada package yang terlewat.

6. Apabila masih ada error, kemungkinan disebabkan karena *package* sudah tidak ada di server. Seperti yang tertera di gambar bawah ini, saat instalasi *package*, Spacemacs di laptop saya tidak bisa instal *package* `evil-escape`. Setelah saya cek di server, ternyata *package* tersebut memang tidak tersedia saat saya instalasi. Oleh karena itu *package* `evil-escape` harus diinstal secara manual. Berikut adalah solusi untuk instal package secara manual:

    ![5-error-evil-escape](/img/tutorial-clojure/bab-3/5f-error-evil-escape.png)

    * Cari repo `evil-escape` di Google. Biasanya tersedia di Github. 
    * *Clone* repo tersebut ke direktori home: 
   
      `$ git clone https://github.com/syl20bnr/evil-escape.git`
   
    * Buka file `~/.spacemacs`. (***Tidak harus memakai Spacemacs, bisa dengan editor teks biasa*.**)
    * Cari baris `dotspacemacs-additional-packages '()` lalu masukkan lokasi direktori `evil-escape` di dalam `'()` sehingga menjadi:
      ```elisp
      dotspacemacs-additional-packages '((evil-escape :location "~/evil-escape"))
      ```
    * *Restart* Spacemacs. Harusnya tidak ada pesan error untuk `evil-escape`, kalau masih ada `warning` yang lain biarkan saja.

7. Kalau masih ada error karena ada *package* yang belum terinstal (misalnya *package* `evil-ediff` di kasus saya), langkahnya mirip dengan sebelumnya:

    ![6-evil-ediff](/img/tutorial-clojure/bab-3/6f-evil-ediff.png)
    
    * Cari reponya di Google, biasanya ada di Github.
    * *Clone* repo ke direktori home: 
        
        `$ git clone https://github.com/emacs-evil/evil-ediff.git`
        
    * Buka file `~/.spacemacs` dan tambahkan lokasi direktori package ke `dotspacemacs-additional-packages '()`. Karena ada 2 *package* yang diinstal secara manual, maka berikut adalah konfigurasi *additional packages* milik saya:
      ```elisp
      dotspacemacs-additional-packages '((evil-escape :location "~/evil-escape") 
                                        (evil-ediff :location "~/evil-ediff"))
      ```

    ![10-packages](/img/tutorial-clojure/bab-3/10f-packages.png)

8. Selanjutnya kita perlu mengaktifkan Spacemacs layer. Layer adalah sekumpulan konfigurasi dan *package* spesifik untuk bahasa pemrograman atau fungsi tertentu. Dalam hal ini, kita perlu mengaktifkan Clojure layer yang akan membantu kita saat menulis program Clojure. Layer bisa diaktifkan secara otomatis saat kita membuka file dengan ekstensi tertentu (contohnya `.clj` untuk Clojure) atau bisa ditambahkan di file `.spacemacs`. Setelah diaktifkan, Spacemacs akan mengunduh *package* tambahan untuk layer yang diaktifkan.

9. Kali ini, kita akan menambahkan layer secara manual lewat file `.spacemacs`. Buka `.spacemacs` dan cari `dotspacemacs-configuration-layer`, yang merupakan *list* yang berisi layer yang sudah terinstal. Tanda `;;` adalah komentar di Lisp, sehingga layer yang didepannya ada `;;` adalah layer nonaktif. Tambahkan `clojure` di *list* tersebut dan aktifkan beberapa layer bawaan jika diperlukan. Seperti pada gambar di bawah, saya juga mengaktifkan layer `auto-completion` dan `better-defaults` . Jika sudah, simpan file `.spacemacs`.

    ![8-dotfile2](/img/tutorial-clojure/bab-3/8f-dotfile2.png)

10. *Restart* Spacemacs agar ia bisa mengunduh *package* untuk Clojure layer. Setelah selesai, Spacemacs siap dipakai untuk Clojure development.

    ![9-instal-layer](/img/tutorial-clojure/bab-3/9f-instal-layer.png)

## IDE Alternatif
Jika tidak ingin memakai Spacemacs, laman resmi Clojure juga punya daftar IDE alternatif yang bisa dipakai untuk Clojure development. Silakan kunjungi https://clojure.org/guides/editors. Biasanya kita perlu memasang plugin tambahan untuk Clojure development. Misalnya di VS code perlu instal [Calva](https://calva.io/). Emacs/Spacemacs sendiri juga ada plugin khusus, yaitu [Cider](https://cider.mx/), tapi proses instalasinya sudah dimudahkan via Clojure layer.

## Sedikit Panduan Pemakaian
Setelah berhasil membangun *environment* untuk Clojure development, kita akan mencoba membuat program Clojure dengan *tools* yang sudah kita instal, khususnya Spacemacs dan Leiningen. Kita akan berinteraksi dengan Spacemacs via *key bindings*, sehingga *mouse* tidak terlalu dibutuhkan. Tidak usah takut karena saya akan memandu dari awal sampai selesai.

1. Pertama, buat *project* Clojure di terminal dengan perintah:
  
   `$ lein new app tes-clojure`

    Perintah di atas kurang lebih berarti "*kita membuat project Clojure baru bernama `tes-clojure` dengan memakai template project `app`*".

    ![11-lein](/img/tutorial-clojure/bab-3/11f-lein.png)

2. Masuk ke direktori `tes-clojure`:
    
    `$ cd tes-clojure`

3. Periksa isi direktori `tes-clojure` dengan perintah `tree`:
  
    `$ tree`

    ![12-tree](/img/tutorial-clojure/bab-3/12f-tree.png)

    Seperti yang dapat dilihat, Leiningen sudah menyiapkan kerangka project untuk kita. Berikut adalah sedikit penjelasan dari direktori atau file yang penting bagi kita:

    * `project.clj`: Berisi konfigurasi *project* seperti dependensi.
    * `resources`: Direktori Untuk menyimpan aset seperti gambar, data, dsb.
    * `src`: Direktori untuk menyimpan *source code* dari *project* kita.
    * `src/tes_clojure/core.clj`: File utama tempat fungsi `main` berada.
    * `test`: Direktori untuk melalukan `testing`.

4. Buka spacemacs GUI. Sebelum melangkah lebih jauh, ada *key binding* yang **sangat penting dan perlu diingat** yaitu **`Ctrl-g`**. *Key binding* ini sangat berguna untuk membatalkan *key binding* yang sedang berjalan. Misalkan *key binding* yang kita inginkan adalah `SPC m ; e`, namun di tengah jalan kita salah ketik menjadi `SPC n`. Di sini kita bisa tekan `Ctrl-g` untuk kembali ke awal, dan memulai *key binding* yang benar, yaitu `SPC m ; e`.

5. Tekan `Spasi f f` secara berurutan membuka file atau membuat baru jika belum ada. (**Setelah ini saya akan singkat tombol Spasi menjadi `SPC`, jadi urutan tombol sebelumnya menjadi `SPC f f`**). Setelah itu akan muncul tampilan seperti di bawah:

    ![13-sff](/img/tutorial-clojure/bab-3/13f-sff.png)

6. Ketikkan nama file yang ingin dibuka. Karena kita ingin mengedit *source code* `tes-clojure`, ketikkan *path* secara lengkap lalu tekan `Enter`. Contohnya di laptop saya ada di *path* `/home/riz/tes-clojure/src/tes_clojure/core.clj`.

    ![14-fname](/img/tutorial-clojure/bab-3/14f-fname.png)

7. Berikut adalah isi dari `core.clj` dan penjelasannya per baris:
    ```clj
    (ns tes-clojure.core                  ;; 1
  	  (:gen-class))                       ;; 2
  	
  	(defn -main                           ;; 3
  	  "I don't do a whole lot ... yet."   ;; 4
  	  [& args]                            ;; 5
  	  (println "Hello, World!"))          ;; 6
  	  
  	  ;; 1. Deklarasi namespace tes-clojure.core. 
  	  ;;    Perhatikan struktur namespace mirip dengan
  	  ;;    struktur direktori src.
  	
  	  ;; 2. Untuk membuat bytecode dari class.
  	  
  	  ;; 3. Definisi fungsi main. 
  	  ;;    Untuk sementara abaikan tanda - di depan main.
  	  
  	  ;; 4. Docstring (opsional).
  	  
  	  ;; 5. Parameter fungsi main. 
  	  ;;    & args berarti ia dapat menerima banyak argumen.
  	  
  	  ;; 6. Isi dari fungsi main.
    ```
8. Selanjutnya, buat fungsi baru di bawah fungsi `main`. Namun sebelumnya, perhatikan warna oranye di **bagian pojok kiri bawah**. Itu berarti kita sedang ada di Window 1, dan warna oranye berarti sedang ada di `normal` mode. 

    ![15-core](/img/tutorial-clojure/bab-3/15f-core.png)

    Karena *editing style* yang saya pilih Vim, maka untuk masuk ke `insert` mode, kita perlu tekan tombol `i` atau `o`. Lalu, masukkan definisi fungsi di bawah ini (perhatikan juga `insert` mode berwarna hijau):

    ```clj
    (defn fungsi-tes
       "tes pembuatan fungsi"
       []     ;; tidak menerima argumen
       (println "halo dari lein"))
    ```

    ![16-fn-baru](/img/tutorial-clojure/bab-3/16f-fn-baru.png)

9. Tekan tombol `Esc` untuk keluar dari `insert` mode lalu tekan `SPC f s` untuk menyimpan file. Perhatikan pesan `Wrote /home/...` di bagian bawah. Itu berarti file sudah berhasil disimpan.

   ![17-saved](/img/tutorial-clojure/bab-3/17f-saved.png)

10. Sekarang kita akan bereksperimen dengan program kita secara interaktif melalui REPL. Pertama, kita harus tekan *maximize window* (pojok kanan atas) agar punya ruang yang cukup untuk *source code* dan REPL.

    ![18-max](/img/tutorial-clojure/bab-3/18f-max.png)

11. Tekan `SPC w 2` untuk membagi Spacemacs menjadi 2 window secara vertikal (Ya, istilah yang dipakai di Emacs adalah window). Nomor window yang sedang aktif adalah oranye/ungu/hijau, sedangkan window nonaktif berwarna hitam. Window 1 kita pakai untuk *source code*, window 2 akan kita pakai untuk REPL.

    ![19-2-window](/img/tutorial-clojure/bab-3/19f-2-window.png)

12. Di window 1, tekan `SPC SPC` (spasi 2x), lalu ketik `cider-jack-in` dan tekan `Enter` untuk menjalankan REPL dan mengkoneksikan Spacemacs dengannya. Perhatikan pesan di bagian bawah. Kita akan melihat pesan `[nREPL] Starting server via...` dan `Connected! ...` saat Spacemacs sudah terhubung dengan REPL.

    ![21-cider-jack](/img/tutorial-clojure/bab-3/21f-cider-jack.png)

    ![22-cider-start](/img/tutorial-clojure/bab-3/22f-cider-start.png)

    ![23-cider-connected](/img/tutorial-clojure/bab-3/23f-cider-connected.png)

13. Pindah ke window 2 dengan perintah `SPC 2`. Perhatikan nomor window 2 jadi berwarna.

14. Di window 2, untuk membuka REPL yang sudah kita jalankan, tekan `SPC b b` untuk melihat daftar buffer yang sedang aktif di Spacemacs. Lalu pilih buffer dengan nama `cider-repl ~/tes...` (ingat pakai tombol kibor, bukan *mouse*).

    ![24-sbb](/img/tutorial-clojure/bab-3/24f-sbb.png)

15. Setelah REPL terbuka, kita perlu ketik `i` sebelum memasukkan perintah ke REPL, karena window 2 berwarna oranye (ingat `normal` mode). Lalu, ketik `(fungsi-tes)` di REPL untuk memanggil `fungsi-tes` yang kita definisikan sebelumnya. Saat dieksekusi, kita akan mendapatkan pesan di REPL.

    ![16-funcall](/img/tutorial-clojure/bab-3/16f-funcall.png)

16. Selanjutnya, mari kita coba panggil `fungsi-tes` melalui `main`. Karena kita masih di `insert` mode di window 2, tekan `Esc` lalu `SPC 1` untuk pindah ke window 1.

17. Di window 1, ketik `i` lalu ubah baris `(println "Hello, World!")` di `main` menjadi `(fungsi-tes)` seperti di bawah. Lalu tekan `Esc` dan `SPC f s` untuk simpan perubahan. 
    ```clj
	 (defn -main
	    "I don't do a whole lot ... yet."
	    [& args]
	    (fungsi-tes))
    ```
19. Akan tetapi, di Clojure kita harus mendefinisikan fungsi dulu sebelum memanggilnya dari fungsi lain. Dalam hal ini kita harus meletakkan `fungsi-tes` di atas `main` (**catatan**: di REPL saat ini kita masih bisa memanggil `fungsi-tes` dari `main` karena sudah **dikompilasi**. Namun, saat kita memulai REPL lagi, *source code* akan dikompilasi dari atas ke bawah dan fungsi `main` akan bingung karena tidak menemukan definisi `fungsi-tes`, sehingga perlu ditaruh di atas `main`). 

20. Kita akan mencoba fitur ***copy-paste*** di Spacemacs. Pertama arahkan kursor ke baris `(defn fungsi-tes`, lalu tekan `Ctrl-V` untuk membuat blok *view* satu baris tersebut (perhatikan perbedaan warna blok dan *background*). Selanjutnya tekan tombol arah bawah untuk membuat blok *view* sampai baris `(println "halo dari lein")`, seperti gambar di bawah.

    ![19-block-view](/img/tutorial-clojure/bab-3/19f-block-view.png)

21. Dengan blok *view* yang masih aktif, tekan tombol `d` untuk *cut* blok tersebut (tombol `y` bisa digunakan untuk *copy*), lalu tempatkan kursor di bawah baris `(:gen-class))` dan tekan tombol `p` untuk *paste*. Silakan rapikan dengan menambahkan baris kosong baru apabila masih kurang rapi.

    ![20-pasted](/img/tutorial-clojure/bab-3/20f-pasted.png)

22. Tekan `SPC f s` untuk menyimpan perubahan.

23. Tekan `SPC m e b` untuk mengevaluasi (*compile*) buffer *source code* ke REPL, sehingga perubahan yang kita buat dapat dikenali oleh REPL. Perhatikan pesan di pojok bawah dan garis hijau disamping nama fungsi seperti pada gambar di bawah. Itu berarti semua definisi fungsi kita berhasil dikompilasi. Sekarang, coba sendiri pindah ke window 2, lalu panggil fungsi `main` dengan perintah `(-main)` di REPL. Lihat apakah ia bisa memanggil `fungsi-tes` atau tidak. Dengan alur kerja seperti ini, kita bisa membuat program secara interaktif.

    ![20-compiled](/img/tutorial-clojure/bab-3/20f-compiled.png)

24. Untuk menonaktifkan REPL, kita bisa tekan `Ctrl-x k` (`Ctrl-x` lalu `k`), lalu pilih buffer REPL (`*cider-repl ...`). Tekan `y` untuk konfirmasi. REPL sudah berhenti.

    ![21-kill-buffer](/img/tutorial-clojure/bab-3/21f-kill-buffer.png)


## Development tanpa Emacs/IDE
Setelah menjelaskan cara memakai Spacemacs untuk membuat program secara dinamis, saya akan menjelaskan alur kerja saat kita tidak memakai IDE yang tidak mendukung Clojure atau hanya dengan editor teks biasa. Sekali lagi saya sarankan untuk memakai editor teks yang sudah punya fitur *parenthesis/bracket matching*, *syntax highlighting*, dan *auto-indentation*.

1. Masuk ke *project* yang sudah dibuat: 

    `$ cd tes-clojure`

2. Jalankan `lein repl` di terminal, lalu pastikan *prompt* yang muncul sesuai dengan namespace *project*, yaitu `tes-clojure.core`

   ![22-lein-repl](/img/tutorial-clojure/bab-3/22f-lein-repl.png)

3. Panggil fungsi `fungsi-tes` (jika belum ada, tulis `fungsi-tes` sesuai dengan definisi di atas):

   ![23-funcall](/img/tutorial-clojure/bab-3/23f-funcall.png)

4. Buka editor teks, dan ubah fungsi `fungsi-tes` di file `core.clj`.
    ```clj 
    (defn fungsi-tes
      "tes pembuatan fungsi"
      []
      (println "halo dari lein repl"))   ;; ubah pesan 
    ```
5. Saat perubahan sudah disimpan dan `fungsi-tes` dipanggil ulang, pesan yang muncul masih sama seperti sebelumnya. Ini berarti kita perlu kompilasi ulang (salah satu kelebihan dari Spacemacs dan mungkin IDE Clojure lain adalah kompilasi tanpa perlu masuk ke REPL). Cara untuk kompilasi ulang file `core.clj` di REPL adalah dengan perintah `(require 'tes-clojure.core :reload)` (**Perhatikan tanda petik di 'tes-clojure.core**). Saat kita panggil `fungsi-tes` lagi setelah kompilasi ulang, kita akan melihat perubahan yang sudah dilakukan.

   ![25-reload](/img/tutorial-clojure/bab-3/25f-reload.png)

6. Silakan ulangi proses di atas (*edit-recompile*) untuk membiasakan diri dengan REPL.

## Sedikit tambahan tentang Lein
Sebagai tambahan, berikut adalah beberapa perintah Leiningen yang berguna saat kita menulis program Clojure:

1. `lein run`: Untuk menjalankan program secara biasa (tanpa REPL).

   ![26-lein-run](/img/tutorial-clojure/bab-3/26f-lein-run.png)

2. `lein uberjar`: untuk kompilasi *project* menjadi *executable*. Dari kompilasi ini kita akan diberi 2 file SNAPSHOT JAR yang berada di direktori `target`: *standalone* dan *non-standalone*. Jalankan file standalone jar dengan perintah berikut. `$ java -jar target/default+uberjar/tes-clojure-0.1.0-SNAPSHOT-standalone.jar`

   ![27-lein-uberjar](/img/tutorial-clojure/bab-3/27f-lein-uberjar.png)

   ![28-jar](/img/tutorial-clojure/bab-3/28f-jar.png)

3. `lein clean`: untuk membersihkan file hasil kompilasi.

4. `lein help`: untuk memeriksa fungsi Leiningen lainnya.

## Penutup
Di tulisan ini, saya harap kalian sudah cukup familiar dengan Emacs/Spacemacs beserta proses *interactive development* via REPL. Apabila tidak suka Spacemacs, silakan pilih IDE lain yang sudah mendukung Clojure development. Di sini saya tidak memakai istilah yang umum dipakai di komunitas Emacs, harapannya setelah ini kalian bisa coba mendalami sendiri. Dokumentasi Spacemacs dapat dipelajari di https://develop.spacemacs.org/doc/DOCUMENTATION.html. Di tulisan berikutnya, saya akan membahas sintaksis Lisp secara umum. Harapannya, setelah terbiasa dengan sintaksis Lisp, materi-materi selanjutnya lebih mudah dipahami.

