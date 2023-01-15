+++
title = "Bagian 4: Struktur Dan Sintaksis"
date = "2023-01-15T11:50:00+07:00"

#
# description is optional
#
description = "Bagian 4: berkenalan dengan struktur dan sintaksis bahasa Clojure"

tags = ["clojure","tutorial",]
+++


Di [bagian 3](https://riz.maulana.me/blog/2022/12/bagian-3-instalasi-dan-cara-pakai-emacs/) kita sudah selesai menyiapkan environment untuk menulis program Clojure. Jika sudah mengikuti panduan di bagian 3, maka seharusnya kamu sudah punya Spacemacs yang akan dipakai sepanjang tutorial ini, atau paling tidak sudah tahu cara mengeksekusi program Clojure dengan IDE lain atau tanpa IDE. Di bagian 4 ini, kita akan belajar struktur sintaksis bahasa Clojure. Seperti yang sudah kita lihat di bagian-bagian sebelumnya, sintaksis Clojure atau Lisp pada umumnya memang sangat berbeda dibandingkan dengan bahasa pemrograman lainnya. Yang paling menonjol adalah banyaknya tanda kurung yang dipakai. Bagi yang belum terbiasa, membaca program Clojure memang cukup membingungkan. Perlu waktu yang relatif tidak sebentar untuk membiasakan diri sampai akhirnya kita bisa menangkap esensi program Clojure di tengah lebatnya rimba parentesis. Harapannya, bagian 4 ini akan memberimu dasar-dasar dari struktur bahasa Clojure dan jadi titik awal untuk memahami lebih jauh.

Sebelum memulai, pastikan kamu sudah menyiapkan REPL agar bisa langsung mencoba kode-kode yang ada bagian ini. Sebagai pengingat, ada 2 cara untuk memulai REPL:
1. Buka terminal, lalu jalankan perintah `clj` atau `clojure`. 
(`clj` lebih direkomendasikan karena fitur untuk REPL lebih banyak)
2. Buka Spacemacs, tekan `SPC SPC` lalu ketik `cider-jack-in` untuk memulai REPL **tanpa Clojure project**. Kemudian buka buffer yang berisi REPL. REPL di Spacemacs memiliki lebih banyak fitur dibanding di CLI dan lebih nyaman digunakan.

> **Tips: Personal workflow**
>
> Biasanya, saya akan membuat sebuah project dulu dengan perintah `lein new app <nama-project>`, lalu buka file `core.clj` project tersebut di Spacemacs. Setelah terbuka, kita bisa mengaktifkan Cider dengan `SPC SPC` -> `cider-jack-in`, dan ia akan otomatis mengenali file `core.clj` sehingga namespace yang muncul sesuai dengan nama project. Di buffer `core.clj` kita bisa menulis kode Clojure **sekaligus** mengeksekusinya di situ juga, **tanpa membuka buffer REPL**. Ada 2 key binding untuk mengeksekusinya:
> * `SPC m e l` untuk mengeksekusi/mengevaluasi baris kode yang kita inginkan.
> * `SPC m e ;` sama seperti di atas, tapi nilai kembaliannya akan ditulis di file `core.clj`, tepat di bawah ekspresi yang dievaluasi.
>
> Menuliskan hasil evaluasi langsung di file akan memudahkan kita memahami kode yang kita buat karena hasil evaluasinya sudah tertera di bawahnya.

> **Penting:**
>
> Jika menemui error di Spacemacs, ia akan menampilkan detail error stack trace dalam window baru. Untuk menutup window tersebut bisa dengan `SPC w d`.


## Struktur Clojure

Program Clojure terdiri dari **ekspresi** atau **form** yang jika dievaluasi/dieksekusi **selalu** memberikan nilai kembalian. Sebagai pembanding, *statement* yang lazim dipakai di imperative programming belum tentu memiliki nilai kembalian dan bersifat layaknya "perintah" (lakukan ini, lakukan itu). Selain itu, terlepas dari banyaknya tanda kurung, struktur kode Clojure sebenarnya sederhana dan seragam. Seragam di sini artinya kode yang kita tulis punya struktur yang sama. Secara umum ada 2 struktur dasar:

1. **Data literal**

    Data literal adalah struktur yang paling sederhana dan dapat berdiri sendiri. Ia adalah representasi literal dari tiap struktur data. Jika dievaluasi, data literal akan swa-evaluasi atau mengembalikan dirinya sendiri. Contoh data literal adalah angka, string, boolean, dan struktur data koleksi seperti list, maps, vector, dan sets. Mari kita coba masukkan beberapa data literal di REPL. Sebagai contoh, bilangan 42 adalah data literal integer dan jika kita ketik `42`, maka ia akan mengembalikan dirinya sendiri. Data literal lain seperti string `"halo"` dan vector `[1 2 3]` jika dievaluasi di REPL juga akan mengembalikan dirinya sendiri.
    ```clojure
    user> 42
    ;; => 42
    user> "halo"
    ;; => "halo"
    user> [1 2 3]
    ;; => [1 2 3]
    ```
    > **Catatan:**
    >
    > `;; =>` adalah penanda baris yang berisi nilai kembalian di REPL.
    
2. **Struktur operasi**

    Menulis program yang hanya berisi data literal tentunya tidak bisa menyelesaikan masalah yang lebih kompleks. Maka dari itu Clojure punya struktur operasi untuk melalukan komputasi terhadap data literal yang kita masukkan. Semua struktur operasi ini memiliki bentuk yang sama:
    
    `(operator argumen-1 argumen-2 ... argumen-n)`

    Struktur seperti ini sering dikenal dengan nama [symbolic expression](https://en.wikipedia.org/wiki/S-expression) atau s-expression/sexpr/sexp dan memang diciptakan untuk keluarga bahasa pemrograman Lisp. Struktur operasi ini diawali dengan tanda kurung lalu diikuti oleh `operator`. Setelah operator, kita dapat memberikan berbagai macam `argumen`, lalu ditutup dengan tutup kurung. `operator` dapat berupa fungsi bawaan atau buatan kita sendiri dan`argumen` yang diterima oleh `operator` dapat berupa data literal atau struktur operasi  lainnya. Selain itu, untuk memisahkan tiap `argumen`, cukup dengan spasi (Clojure juga memperlakukan koma layaknya spasi). Seperti yang saya singgung sebelumnya, struktur operasi ini harus dalam notasi prefiks, yang artinya operator harus dan selalu ada di paling kiri setelah buka kurung. Struktur operasi ini juga selalu mengembalikan nilai hasil evaluasi.
    
    
Dari 2 struktur sederhana di atas, kita bisa membuat program Clojure yang berisi ekspresi yang lebih kompleks. Tapi, sebagai langkah awal, mari kita mulai dengan contoh sederhana, yaitu ekspresi `(* 7 6)`. Di ekspresi tersebut, kita ingin melalukan operasi perkalian bilangan `7` dan `6` dengan operator `*`. Jika dievaluasi, maka prosesnya sebagai berikut:
* `7` dan `6` akan mengembalikan dirinya sendiri.
* Simbol `*` akan merujuk ke obyek fungsi perkalian di dalam Clojure.
* Kembalian dari `7` dan `6` akan dipakai sebagai argumen lalu melakukan perkalian.
* Nilai `42` dikembalikan sebagai hasil evaluasi

Sebagai catatan, operator `*` tidak memiliki batasan jumlah argumen sehingga kita bisa menambahkan bilangan yang ingin kita operasikan, contohnya `(* 7 6 10)` atau `(* 7 6 5 2 4)`. Inilah salah satu kelebihan notasi prefiks: kita tidak perlu mengetik ulang operator sesuai jumlah operasi yang diinginkan. Selain itu, kita juga bisa menyisipkan ekspresi di dalam ekspresi seperti `(* 7 6 (+ 1 3))` yang sama seperti `7 * 6 * (1 + 3)`.

```clojure
user> (* 7 6)
;; => 42
user> (* 7 6 10)
;; => 420
user> (* 7 6 5 2 4)
;; => 1680
user> (* 7 6 (+ 1 3))
;; => 168
```

Berbicara soal ekspresi di Clojure, ada hal penting yang perlu diketahui, yaitu **side effects** atau **efek samping**. Jadi, **tiap ekspresi** di Clojure akan menghasilkan nilai kembalian. Apabila ia melakukan sesuatu **selain mengembalikan nilai**, itu namanya efek samping. Efek samping ini dapat berupa mengirimkan data via Internet, menyimpan data di basis data, atau yang paling sederhana menampilkan data di layar monitor.
    
Sebentar... kok bisa menampilkan data di monitor disebut sebagai efek samping? Saya akan coba jelaskan dengan contoh di REPL. Coba ketikkan ekspresi di bawah ini di REPL:

```clojure
user> (str "ini " "nilai " "kembalian")
;; => "ini nilai kembalian"
```

Jika diperhatikan, fungsi `str` mengembalikan string hasil penggabungan argumennya. Nilai kembalian ini memang ditampilkan di monitor karena kita sedang ada di REPL (masih ingat arti huruf P di REPL?). Jadi, di sini ia hanya mengembalikan string dan tidak ada efek samping. Sekarang coba evaluasi ekspresi di bawah:

```clojure
user> (println "apa" "ini" "nilai" "kembalian?")
;; apa ini nilai kembalian?
;; => nil
```

Setelah dievaluasi, kita lihat ada 2 baris yang muncul, `apa ini nilai kembalian?` dan `nil`. Apakah string `apa ini nilai kembalian?` nilai kembalian? Jawabannya adalah **bukan**. Fungsi `println`  digunakan untuk menampilkan informasi (yang mana termasuk efek samping), sehingga string `apa ini nilai kembalian?` adalah informasi yang ditampilkan di standard output atau monitor. Tapi, karena semua ekspresi/fungsi di Clojure harus menghasilkan nilai kembalian, kita bisa lihat `nil` di bawahnya dan inilah nilai kembalian fungsi `println`. Jadi, di Clojure ada banyak fungsi seperti `println` yang tugas utamanya adalah menghasilkan efek samping dan mengembalikan `nil`. Fungsi dengan efek samping ini sering dinamakan *impure functions*  atau fungsi tak-murni, sedangkan fungsi yang hanya mengembalikan nilai dinamakan *pure functions* atau fungsi murni. Konsep efek samping ini harusnya tidak terlalu asing karena di bahasa pemrograman lain juga mungkin secara tidak sadar sering kita temui (misalnya saat membuat fungsi dengan keyword `void`).

## Kode adalah Data
Dari penjelasan struktur bahasa Clojure di atas, kita bisa menyatakan bahwa kode atau ekspresi di Clojure (dan Lisp pada umumnya) juga termasuk data. Apa maksudnya? Maksudnya, ekspresi seperti `(println "halo" 123)`, selain melakukan sesuatu jika dievaluasi, ia juga struktur data list yang elemennya terdiri dari `println`, `"halo"`, dan `123`. Di sinilah tanda kurung berperan sangat penting untuk membatasi ruang lingkup ekspresi *sekaligus* mengelompokkan elemen ke dalam list. Jadi, bisa dibilang program Clojure yang kita tulis juga merupakan struktur data list yang besar dan berisi data literal, fungsi, dsb. Dari sini seharusnya bisa dipahami kenapa kepanjangan dari Lisp adalah List Processor. Karakteristik "kode adalah data" ini disebut dengan istilah [homoiconicity](https://en.wikipedia.org/wiki/Homoiconicity). Karakteristik ini sangat bermanfaat saat kita ingin menambahkan sendiri fungsionalitas yang tidak kita temukan di Clojure dengan membuat makro. Makro adalah sebuah program yang dapat menghasilkan program lain dan ia akan dibahas di tulisan yang akan datang.

## Data literal List
Ada satu hal lagi yang perlu dijelaskan terkait sintaksis Clojure. Struktur operasi seperti `(println 1 2 3)`, selain dapat diperlakukan sebagai list, ia juga **secara default** dianggap function call atau pemanggilan fungsi. Dalam hal ini, kita memanggil fungsi `println` dengan argumen `1`, `2`, dan `3`. Mari kita buat contoh lain, misalnya kita berkeinginan untuk membuat struktur data list berisi integer seperti `(4 5 6)`. Sekarang coba ketikkan list tersebut ke REPL dan perhatikan apa yang terjadi. Saya jamin pasti error. Ini terjadi karena list yang kita masukkan dianggap sebagai function call; `4` dianggap sebagai sebuah fungsi (yang mana bukan), `5` dan `6` dianggap sebagai argumennya. 
```clojure 
user> (4 5 6)
;; Execution error (ClassCastException) at user/eval7564 (REPL:105).
;; class java.lang.Long cannot be cast to class clojure.lang.IFn 
;; (java.lang.Long is in module java.base of loader 'bootstrap'; 
;; clojure.lang.IFn is in unnamed module of loader 'app')
```
> Di Spacemacs, ketik `SPC w d` untuk menutup window berisi error stacktrace.

Lalu bagaimana cara kita mendapatkan representasi list-nya? Kita harus memaksa Clojure untuk tidak mengevaluasinya sebagai function call dengan cara menambahkan tanda petik satu `'` sebelum buka kurung. Atau kita bisa memanggil fungsi `quote` yang dapat mengembalikan argumen tanpa dievaluasi:

```clojure
user> '(4 5 6)
;; => (4 5 6)
user> (quote (4 5 6))
;; => (4 5 6)
user> (quote 12)
;; => 12
user> (println 1 2 3)
;; 1 2 3
;; => nil
user> '(println 1 2 3)
;; => (println 1 2 3)
user> (quote (println 1 2 3))
;; => (println 1 2 3)
```
Dengan cara di atas, kita bisa mendapatkan struktur data list tanpa dianggap sebagai sebuah function call oleh Clojure.

## Konvensi Gaya Penulisan
Sebelum menutup bagian 4 ini, saya perlu menjelaskan konvensi yang dipakai untuk menulis program Clojure. Program Clojure ditulis dengan gaya `kebab-case` yang berarti tiap kata dipisahkan dengan tanda pisah `-` dan dalam huruf kecil. Penggunaan gaya `kebab-case` ini bertujuan agar lebih mudah dibaca dan disarankan untuk tidak menyingkat kata secara berlebihan. Jadi, kalau ingin membuat variabel untuk menyimpan umur kita tahun ini, kita bisa menamainya dengan `umur-saya-tahun-ini`. Atau, kalau ingin membuat fungsi untuk menghitung luas lingkaran, kita bisa menamainya `hitung-luas-lingkaran`. Nantinya kita juga akan menemui konvensi lain seperti penggunaan tanda tanya `?` dan seru `!` di akhir nama variabel, tapi untuk sementara itu dulu yang perlu diketahui.

## Penutup
Di bagian 4 ini kita belajar tentang struktur bahasa Clojure. Kita sudah melihat bahwa struktur Clojure sangat sederhana dan seragam. Selain itu kita juga melihat bahwa kode Clojure bisa dianggap sebagai struktur data list yang nantinya bisa kita proses untuk menciptakan fungsionalitas yang belum ada. Di bagian selanjutnya, kita akan mulai berkenalan dengan berbagai macam tipe data yang ada di Clojure.
