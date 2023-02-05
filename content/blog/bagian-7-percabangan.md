+++
title = "Bagian 7: Percabangan"
date = "2023-02-05T14:29:36+07:00"

#
# description is optional
#
description = "Bagian 7: Mengenal cara mengontrol evaluasi dengan percabangan di Clojure"

tags = ["clojure","tutorial",]
+++


Di **bagian 6**, kita sudah mengenal struktur data koleksi di Clojure yang bisa digunakan untuk menghimpun data heterogen beserta operasi dasar yang bisa dilakukan kepadanya. Sampai titik ini, program yang kita tulis masih berupa pemanggilan fungsi biasa yang kebanyakan hanya satu baris. Nantinya, program Clojure kita akan berkembang menjadi ratusan baris bahkan lebih. Secara default, Clojure akan mengevaluasi ekspresi dari atas sampai bawah secara berurutan. Tentunya ini tidak ideal karena kita ingin mengontrol alur evaluasi program Clojure yang kita buat. Di sinilah **percabangan** atau **struktur kontrol** berperan penting untuk membantu kita menentukan alur program berdasarkan suatu kondisi. 

Di bagian ini kita akan mengenal cara membuat percabangan dalam program Clojure. Percabangan di Clojure tidak jauh berbeda dengan yang ada di bahasa pemrograman lain. Namun, perlu diingat bahwa percabangan juga merupakan ekspresi yang akan mengembalikan nilai. Jika dalam suatu cabang terdapat banyak ekspresi, maka ekspresi terakhir yang akan dikembalikan.

> **Tips: cara cek history ekspresi**
> 
> Di bagian ini, kita akan menulis program Clojure dalam banyak baris (sebenarnya bisa dibuat satu baris tapi tidak enak dibaca). Idealnya, program tersebut ditulis di dalam file. Namun, kita masih akan bermain di REPL dulu sampai 3-4 bagian lagi. 
> Agar tidak mengetik ulang ekspresi di REPL, gunakan tombol panah atas `↑` di `lein repl` atau `CTRL+↑` di Spacemacs untuk mengakses *history* ekspresi yang pernah kita tulis. Selain itu, untuk membuat baris baru di REPL, gunakan `Ctrl+Enter`.
>
> Di Spacemacs, *history* ekspresi disimpan dalam satu kesatuan, bukan per baris. Jadi, jika sudah menulis ekspresi yang panjangnya 10 baris, maka dengan `CTRL+↑` kita akan dapatkan 10 baris itu. Tinggal ubah bagian mana saja yang ingin dimodifikasi tanpa perlu mengetik ulang dari awal.

## Pemanasan: `let` binding
Sebelum masuk ke materi utama percabangan, mari kita berkenalan lebih lanjut dengan fungsi `let`. Sebelumnya kita sudah melihatnya di [bagian 5](https://riz.maulana.me/blog/2023/01/bagian-5-tipe-data-dasar/). Fungsi `let` digunakan untuk membuat *binding* yang bersifat lokal (gampangnya, untuk membuat **variabel lokal**). *Binding* yang ada di dalam `let` juga bersifat *immutable* dan *persistent* serta tidak bisa diakses dari luar *scope* `let`. 

Berikut adalah contoh `let` *binding*, di mana kita *bind* nilai `42` dengan symbol `x` di dalam vector (di bahasa lain, ini sama seperti `x = 42`). 

```clojure 
user> (let [x 42] ;; bind x dan 42 dalam vector (sintaksisnya memang seperti itu).
        x)        ;; scope x hanya sampai sini, dan jadi nilai kembalian.
;; => 42
```

Setelah vector untuk *binding*, symbol `x` sudah terasosiasi dengan nilai `42` dan kita bisa tulis ekspresi apapun yang memanfaatkan `x`. Ekspresi paling akhir dalam `let` akan menjadi nilai kembalian. Dalam hal ini, karena hanya ada 1 ekspresi (yaitu `x`), maka nilai `x` akan dikembalikan.

Kita bisa membuat banyak *binding* di dalam `let`. Selain itu, tiap *binding* juga bisa digunakan untuk *binding* selanjutnya. Berikut contohnya:

```clojure 
user> (let [x 10
            y (+ x 3)] ;; pakai binding sebelumnya.
        x              ;; x dievaluasi, tapi tidak dikembalikan.
        y)             ;; y jadi nilai kembalian.
;; => 13
```
Secara default, `let` akan mengembalikan `nil` jika tidak ada nilai kembalian. Nilai kembalian pun bisa kita buat sesuai dengan keinginan kita, dan tidak harus mengembalikan nilai dalam *binding*.

```clojure 
user> (let [x 4
            x-kuadrat (* x x)
            x-kubik (* x x-kuadrat)]
        (println (str "nilai dari " x " kuadrat dan kubik adalah " x-kuadrat " dan " x-kubik))
        :evaluasi-selesai)  ;; mengembalikan nilai arbitrer
;; nilai dari 4 kuadrat dan kubik adalah 16 dan 64
;; => :evaluasi-selesai
```

Di bagian ini, `let` *binding* akan digunakan sebagai simulasi saat kita memberikan argumen ke fungsi. Karena kita belum masuk ke materi fungsi, `let` *binding* bisa jadi pengganti sementara.

Mari kita mulai belajar percabangan di Clojure mulai dari yang paling sederhana, yaitu fungsi `if`.

## Percabangan `if`
Fungsi `if` adalah bentuk percabangan dasar di Clojure. Ia juga disebut sebagai salah satu **special form**. Seperti yang kita tahu, aturan evaluasi standar di Clojure adalah mengevaluasi seluruh argumen dalam suatu ekspresi. Hasil evaluasi ini akan diterapkan ke operator yang ada di sebelah paling kiri. *Special form* punya aturan evaluasi tersendiri yang berbeda dengan aturan standar (makanya dinamakan *special form*). Dalam hal ini, `if` tidak akan mengevaluasi semua argumennya. Proses evaluasi dalam `if` bergantung pada pengecekan kondisi. 

Berikut adalah sintaksis `if`:
```clojure 
(if <kondisi> 
    <ekspresi-jika-benar> 
    <ekspresi-jika-salah>)
```

Dibandingkan dengan `if` di bahasa pemrograman lain, `if` di Clojure hanya bisa membentuk dua percabangan untuk salah dan benar serta tidak mendukung bentuk `else`. Jika ingin membuat percabangan lebih dari dua, kita harus membuat `if` lagi di dalam salah satu cabang (`if` bersarang, nanti kita akan melihat contohnya). Sebagai alternatif, Clojure sudah menyediakan fungsi `cond` yang bisa digunakan untuk membuat banyak percabangan dan akan dibahas di bawah.

Selain itu, `<ekspresi-jika-salah>` bersifat opsional dan boleh tidak dimasukkan dalam `if` jika hanya memerlukan `<ekspresi-jika-benar>`. Berikut adalah beberapa contoh percabangan dengan `if`, di mana `<kondisi>` menggunakan data literal yang bernilai `true` atau `false`:

```clojure 
user> (if true 
        (println "benar")
        (println "salah"))
;; benar
;; => nil

user> (if false 
        (println "benar")
        (println "salah"))
;; salah
;; => nil

user> (if nil 
        (println "benar")
        (println "salah"))
;; salah
;; => nil

user> (if '()               ;; list kosong dianggap true
        (println "benar")
        (println "salah"))
;; benar
;; => nil
```

Berikut adalah contoh `if` di mana `<kondisi>` adalah ekspresi yang mengembalikan boolean:

```clojure 
user> (if (< 4 5) 
        (println "4 lebih kecil dari 5")
        (println "4 lebih besar dari 5"))
;; 4 lebih kecil dari 5
;; => nil

user> (if (> 4 5) 
        (println "4 lebih kecil dari 5")
        (println "4 lebih besar dari 5"))
;; 4 lebih besar dari 5 ;; SALAH
;; => nil
```

Dari contoh di atas, kita bisa melihat bahwa tiap cabang hanya berisi satu ekspresi (`println` untuk contoh di atas). Jika yang diinginkan adalah banyak ekspresi dalam tiap cabang, kita bisa "membungkusnya" dengan fungsi `do`. Dengan `do`, semua ekspresi yang ada di dalamnya akan dievaluasi berurutan dan dianggap sebagai satu blok ekspresi. Selain itu, hasil evaluasi dari ekspresi terakhir di dalam `do` akan dikembalikan. Fungsi `do` bermanfaat jika kita ingin melakukan banyak operasi dalam satu cabang dan biasanya operasi tersebut melibatkan *side effects* seperti mengirimkan data via Internet lalu menampilkan status pengirimannya.

```clojure 
user> (if :boleh-dikirim ;; dianggap true
        (do
          (println "Mengirim data...")
          (println "Data telah terkirim")
          :pesan-terkirim)
        (do
          (println "Data tidak boleh dikirim")
          (println "Membatalkan operasi...")))
;; Mengirim data...
;; Data telah terkirim
;; => :pesan-terkirim

user> (if false
        (do
          (println "Mengirim data...")
          (println "Data telah terkirim")
          :pesan-terkirim)
        (do
          (println "Data tidak boleh dikirim")
          (println "Membatalkan operasi...")))
;; Data tidak boleh dikirim
;; Membatalkan operasi...
;; => nil                   ;; println mengembalikan nil
```

### Alternatif: `when` dan `when-not`
Jika yang kita pedulikan hanya satu cabang saja (benar atau salah), Clojure punya fungsi alternatif yang bisa menangani kasus tersebut, yaitu `when` dan `when-not`.

`when` bisa digunakan jika kita ingin mengevaluasi ekspresi hanya saat kondisi bernilai `true`. Jika `false`, ekspresi akan diabaikan. Fungsi `do` tidak dibutuhkan untuk mengelompokkan ekspresi menjadi satu karena `when` akan mengevaluasi semua ekspresi saat kondisi bernilai `true`.


```clojure 
(when <kondisi-true> <expr>)
```

```clojure 
user> (when true
        (println "ini ditampilkan saat true.")
        (println "akan diabaikan saat false.")
        :kembalian)
;; => ini ditampilkan saat true.
;; => akan diabaikan saat false.
;; => :kembalian

user> (when false
        (println "ini ditampilkan saat true.")
        (println "akan diabaikan saat false.")
        :kembalian)
;; => nil
```

Kebalikan dari `when` adalah `when-not`, yang akan mengevaluasi cabang hanya saat `false`.

```clojure 
(when-not <kondisi-false> <expr>)
```

```clojure 
user> (when-not true
        (println "blok ini ditampilkan saat false.")
        (println "akan diabaikan saat true.")
        :kembalian)
;; => nil

user> (when-not false
        (println "blok ini ditampilkan saat false.")
        (println "akan diabaikan saat true.")
        :kembalian)
;; blok ini ditampilkan saat false.
;; akan diabaikan saat true.
;; => :kembalian
```

Sekarang, mari kita buat percabangan lebih dari 2 dengan `if`. Contoh yang akan saya buat adalah pemeriksaan bilangan apakah ia bilangan negatif, positif, atau nol dengan fungsi `pos?` dan `neg?` (yang berarti akan ada 3 cabang). Percabangan tersebut akan ditempatkan di dalam `let`.


```clojure 
user> (let [x -1]          ;; bind x dengan -1
        (if (pos? x)       ;; apakah x positif?
          :nilai-positif   ;; jika benar
          (if (neg? x)     ;; jika salah, apakah x negatif?
            :nilai-negatif ;; jika benar
            :nol)))        ;; jika salah
;; => :nilai-negatif

user> (let [x 0]
        (if (pos? x)
          :nilai-positif
          (if (neg? x)
            :nilai-negatif
            :nol)))
;; => :nol
```
Dari contoh di atas, `if` bisa menangani percabangan lebih dari 2. Namun, program yang kita tulis jadi kurang enak dibaca (paling tidak bagi saya). Bisa dibayangkan bagaimana jadinya jika ada lebih banyak percabangan yang harus ditulis dalam `if`.

Mari kita buat contoh lagi dengan lebih banyak cabang. Kali ini saya ambil contoh perhitungan [*body mass index*](https://en.wikipedia.org/wiki/Body_mass_index) atau BMI, yang mana kategori BMI diambil dari Wikipedia seperti tabel di bawah. Percabangan yang dibuat akan menentukan kategori BMI berdasarkan nilai BMI yang kita masukkan.


**Tabel BMI**

| Kategori | BMI (kg/m^2) |
| -------- | ------------ |
| Underweight (Severe thinness) | < 16 |
| Underweight (Moderate thinness) | 16.0 – 16.9 |
| Underweight (Mild thinness) | 17.0 – 18.4  |
| Normal range | 18.5 – 24.9  |
| Overweight (Pre-obese) | 25.0 – 29.9 |
| Overweight (Class I) | 30.0 – 34.9 |
| Overweight (Class II) | 35.0 – 39.9 |
| Overweight (Class III) | ≥ 40.0  |


Berikut adalah percabangan BMI dengan `if`:

```clojure 
user> (let [bmi 18.4]   ;; ganti binding untuk tes nilai lainnya
        (if (< bmi 16.0)
          "Underweight (Severe thinness)"
          (if (<= 16.0 bmi 16.9)
            "Underweight (Moderate thinness)"
            (if (<= 17.0 bmi 18.4)
              "Underweight (Mild thinness)"
              (if (<= 18.5 bmi 24.9)
                "Normal range"
                (if (<= 25.0 bmi 29.9)
                  "Overweight (Pre-obese)"
                  (if (<= 30.0 bmi 34.9)
                    "Obese (Class 1)"
                    (if (<= 35.0 bmi 39.9)
                      "Obese (Class II)"
                      "Obese (Class III)"))))))))
;; => "Underweight (Mild thinness)"
```
Karena `if` hanya punya 2 cabang, di tiap `<ekspresi-jika-salah>` harus dilakukan pengecekan kebenaran lagi dengan `if`. Meskipun bekerja, program BMI di atas kurang enak dibaca. Untuk menangani kasus tersebut, Clojure menyediakan fungsi `cond` yang sintaksisnya lebih mudah dipahami.


## Percabangan banyak dengan `cond`
Dengan fungsi `cond`, kita bisa membuat percabangan banyak secara sederhana. Ia menerima beberapa pasang ekspresi, misalnya sepasang ekspresi `kondisi-1` dan `ekspresi-1`. Ekspresi `kondisi-1` harus bernilai `true` agar bisa mengevaluasi `ekspresi-1`. Jika tidak ada ekspresi `<kondisi>` yang bernilai `true`, maka `cond` akan mengembalikan `nil`. Berikut adalah sintaksis `cond`:

```clojure 
(cond 
    <kondisi-1> <ekspresi-1>
    <kondisi-2> <ekspresi-2>
    ...
    <kondisi-n> <ekspresi-n>)
```

Berikut adalah contoh sederhana percabangan dengan `cond` yang dibungkus di dalam `let` binding.

```clojure 
user> (let [x 10]
        (cond 
          (< x 0) "x bernilai negatif" ;; satu pasang ekspresi
          (< x 50) "x di bawah 50"     ;; satu pasang ekspresi
          (< x 100) "x di bawah 100")) ;; satu pasang ekspresi
;; => "x di bawah 50"

user> (let [x -5]
        (cond 
          (< x 0) "x bernilai negatif"
          (< x 50) "x di bawah 50"
          (< x 100) "x di bawah 100"))
;; => "x bernilai negatif"

user> (let [x 110]
        (cond 
          (< x 0) "x bernilai negatif"
          (< x 50) "x di bawah 50"
          (< x 100) "x di bawah 100"))
;; => nil
```
Agar bisa menangani *default case*, maka kita memerlukan satu pasang ekspresi lagi, yang mana `<kondisi>` **harus** bernilai `true`. Ia bisa dalam nilai apapun selain `false` atau `nil`. Nilai yang sering dipakai adalah `:else` atau `:otherwise`, yang mana kedua nilai tersebut sama-sama bernilai `true`.

```clojure 
user> (let [x 110]
        (cond 
          (< x 0) "x bernilai negatif"
          (< x 50) "x di bawah 50"
          (< x 100) "x di bawah 100"
          :else "nilai di atas 100")) 
;; => "nilai di atas 100"
```
Setelah mengenal bagaimana `cond` bekerja, mari kita modifikasi contoh pemeriksaan bilangan dan BMI di atas agar menggunakan `cond`.

```clojure 
user> (let [x -1]
        (cond
          (pos? x) :nilai-positif
          (neg? x) :nilai-negatif
          :else :nol))
;; => :nilai-negatif

user> (let [bmi 18.4]
        (cond 
          (< bmi 16.0) "Underweight (Severe thinness)"
          (<= 16.0 bmi 16.9) "Underweight (Moderate thinness)"
          (<= 17.0 bmi 18.4) "Underweight (Mild thinness)"
          (<= 18.5 bmi 24.9) "Normal range"
          (<= 25.0 bmi 29.9) "Overweight (Pre-obese)"
          (<= 30.0 bmi 34.9) "Obese (Class I)"
          (<= 35.0 bmi 39.9) "Obese (Class II)"
          :else "Obese (Class III)"))
;; => "Underweight (Mild thinness)"
```
Jika dibandingkan dengan contoh `if`, percabangan dengan `cond` ini terlihat lebih rapi dan enak dibaca karena tiap cabang dikelompokkan dalam satu pasang ekspresi. Jika kita nanti bertemu dengan kasus yang mempunyai cabang banyak, fungsi `cond` sangat disarankan untuk dipakai.


## `case`: Percabangan berdasarkan konstanta
Fungsi `case` di Clojure mirip seperti fungsi `switch-case` di bahasa C. Jika fungsi `cond` *memeriksa kebenaran dari kondisi*, fungsi `case` akan *memeriksa nilai/konstanta* untuk melakukan percabangan. Jika nilai/konstanta sesuai dengan yang kita inginkan, kita bisa mengevaluasi ekspresi pasangannya (sama seperti `cond`). Jadi, ekspresi kondisi tidak harus `true` atau `false`, tapi nilai lainnya juga bisa diperiksa. Berikut adalah perbandingan `cond` dan `case` untuk kasus yang sama.

```clojure 
user> (let [x 20]
        (cond 
          (= x 10) :sepuluh
          (= x 20) :dua-puluh
          (= x 30) :tiga-puluh
          :else "bukan 10, 20, atau 30"))
;; => :dua-puluh

user> (let [x 40]
        (cond 
          (= x 10) :sepuluh
          (= x 20) :dua-puluh
          (= x 30) :tiga-puluh
          :else "bukan 10, 20, atau 30"))
;; => "bukan 10, 20, atau 30"

user> (let [x 30]
        (case x 
          10 :sepuluh
          20 :dua-puluh
          30 :tiga-puluh
          "bukan 10, 20, atau 30")) ;; default case
;; => :tiga-puluh

user> (let [x 50]
        (case x 
          10 :sepuluh
          20 :dua-puluh
          30 :tiga-puluh
          "bukan 10, 20, atau 30")) ;; default case
;; => "bukan 10, 20, atau 30"
```
Contoh program BMI di atas tidak cocok memakai `case` karena mengharuskan kita untuk memeriksa semua nilai, yang mana jumlahnya tidak terhingga. Sehingga, `cond` lebih cocok dipakai. Namun, jika kita sudah tahu nilai apa saja yang perlu diperiksa, lebih baik pilih `case` daripada `cond` karena `case` bekerja lebih cepat. Kita bisa mencoba mengukur seberapa cepat suatu ekspresi dievaluasi dengan fungsi `time`. Fungsi `time` hanya menerima satu argumen, yaitu ekspresi yang akan diukur waktu evaluasinya. Mari kita bandingkan kinerja `case` dan `cond` untuk contoh di atas.

```clojure 
user> (time
       (let [x 30]
         (case x 
           10 :sepuluh
           20 :dua-puluh
           30 :tiga-puluh
           "bukan 10, 20, atau 30")))
;; "Elapsed time: 0.025528 msecs"
;; => :tiga-puluh

user> (time 
       (let [x 30]
         (cond 
           (= x 10) :sepuluh
           (= x 20) :dua-puluh
           (= x 30) :tiga-puluh
           :else "bukan 10, 20, atau 30")))
;; "Elapsed time: 0.027662 msecs"
;; => :tiga-puluh
```

Dari contoh di atas, kita bisa melihat `case` sedikit lebih cepat dibanding `cond`. Perbedaannya tidak seberapa tapi perlu diingat bahwa itu contoh kecil. Selain lebih cepat, `case` lebih singkat dan enak dibaca. 

## Penutup
Di bagian ini kita sudah mengenal cara mengontrol evaluasi di Clojure dengan `if`, `when`, `when-not`, `cond`, dan `case`. `if` cocok dipakai untuk percabangan yang tidak banyak (maksimal 2), sedangkan `cond` lebih cocok untuk percabangan banyak. Selain itu, dengan `case`, kita bisa mengevaluasi ekspresi berdasarkan suatu nilai yang tidak harus boolean. Dengan dukungan fungsi-fungsi percabangan di atas, kita bisa mengontrol proses evaluasi dan membuat program Clojure yang lebih kompleks.

Di bagian berikutnya, kita akan belajar cara membuat perulangan di Clojure.
