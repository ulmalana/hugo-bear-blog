+++
title = "Bagian 9-a: Fungsi"
date = "2023-03-25T13:30:52+07:00"

#
# description is optional
#
# description = "An optional description for SEO. If not provided, an automatically created summary will be used."

tags = ["clojure","tutorial",]
+++

Sebagai bahasa pemrograman **fungsi**onal, fungsi di Clojure berperan sangat penting dan menjadi *first class citizens*. Artinya, fungsi dapat diperlakukan layaknya nilai biasa: ia dapat menjadi argumen atau nilai kembalian dari fungsi lainnya. Karakteristik ini sering dinamai sebagai ***higher-order functions*** atau fungsi orde tinggi. 

Bagian 9 ini dipecah menjadi beberapa bagian agar tidak menjadi terlalu panjang. Di bagian 9-a ini, kita akan belajar membuat dan menggunakan fungsi di Clojure. Kita akan mulai dari *anonymous function* atau fungsi anonim terlebih dahulu.

## Fungsi anonim
Sesuai dengan namanya, fungsi anonim adalah fungsi yang tidak memiliki nama. Karena tidak memiliki nama, kita tidak bisa merujuk atau memanggilnya. Seringkali fungsi anonim digunakan untuk komputasi sederhana dan sekali pakai.

Untuk membuat fungsi anonim, kita menggunakan fungsi `fn` yang diikuti oleh parameter fungsi dan badan fungsi, seperti di bawah ini.

```clojure 
(fn [parameter*] badan-fungsi)
```
Parameter pada fungsi anonim dideklarasikan di dalam vector dan tidak ada batasan jumlah (bahkan tanpa kita beri parameter pun masih tetap valid). Berikut adalah beberapa contoh fungsi anonim:

```clojure 
;; fungsi anonim tanpa parameter
user=> (fn [] (println "tanpa parameter"))
;; => #object[user$eval2071$fn__2072 0x4280e823 "user$eval2071$fn__2072@4280e823"]


;; fungsi anonim dengan 1 parameter 
;; untuk menghitung nilai kuadrat
user=> (fn [x] (* x x))
;; => #object[user$eval2075$fn__2076 0x471c1337 "user$eval2075$fn__2076@471c1337"]

;; fungsi anonim dengan 2 parameter
;; untuk menjumlahkan 2 parameter tersebut
user=> (fn [x y] (+ x y))
;; => #object[user$eval2079$fn__2080 0x40cbfe52 "user$eval2079$fn__2080@40cbfe52"]
```

Contoh di atas hanya menunjukkan cara membuat fungsi anonim sehingga nilai kembaliannya adalah sebuah object yang berisi fungsi. Cara alternatif untuk membuat fungsi anonim adalah dengan literal fungsi. Layaknya struktur koleksi list dan vector yang memiliki bentuk literal dengan `(...)` dan `[...]`, fungsi di Clojure juga mendukung bentuk literal dengan `#(...)`. Fungsi literal tersebut dapat menyingkat penulisan dan seringkali lebih diutamakan dalam membuat operasi sederhana. Dengan literal fungsi, kita bisa mengubah contoh di atas menjadi:

```clojure 
user=>  #(println "tanpa parameter")
;; => #object[user$eval2088$fn__2089 0x374090c3 "user$eval2088$fn__2089@374090c3"]

user=> #(* %1 %1) ;; atau bisa juga #(* % %)
;; => #object[user$eval2093$fn__2094 0x80b0315 "user$eval2093$fn__2094@80b0315"]

user=> #(+ %1 %2)
;; => #object[user$eval2099$fn__2100 0x3a351a67 "user$eval2099$fn__2100@3a351a67"]
```
> Catatan
>
> Meskipun hanya berbeda satu karakter `#`, ekspresi `#(println "tanpa parameter")` dan `(println "tanpa parameter")` tidaklah sama.
> `#(println "tanpa parameter")` adalah fungsi anonim tanpa parameter dan jika dievaluasi akan mengembalikan object fungsi. Sementara itu, `(println "tanpa parameter")` adalah *function call* yang akan menghasilkan `side-effect` (menampilkan `"tanpa parameter"` di layar).


Dengan literal fungsi, kita tidak perlu mendeklarasikan parameter yang akan kita pakai di dalam fungsi. Tanda persen `%n` di atas menjadi *placeholder* dari parameter dan langsung ditempatkan sebagai argumen (jika masih bingung dengan perbedaan parameter dan argumen, silakan kunjungi https://developer.mozilla.org/en-US/docs/Glossary/Parameter). Sebagai contoh, `%1` berlaku sebagai parameter pertama, `%2` berlaku sebagai parameter kedua, dan seterusnya. Jika yang dipakai hanya tanda persen saja `%`, maka ia dianggap merepresentasikan parameter pertama (`%` = `%1`).

Contoh-contoh di atas hanya membuat fungsi anonim saja. Lalu bagaimana cara memakai fungsi anonim tersebut? Perlu diingat kembali bahwa fungsi atau operator pada *function call* ada di sebelah paling kiri seperti `(+ 1 2)`. Fungsi anonim juga mengikuti aturan yang sama jika kita ingin memanggil mereka. Berikut adalah contoh *function call* untuk fungsi-fungsi anonim di atas, termasuk dalam bentuk literal.

```clojure 
;; fungsi anonim perlu dibungkus di dalam (...) agar bisa dipanggil
user=> ((fn [] (println "tanpa parameter")))
;; tanpa parameter
;; => nil
user=> (#(println "tanpa parameter"))
;; tanpa parameter
;; => nil

user=> ((fn [x] (* x x)) 5)
;; => 25
user=> (#(* % %) 5)
;; => 25

user=> ((fn [x y] (+ x y)) 6 8)
;; => 14
user=> (#(+ %1 %2) 6 8)
;; => 14
```
Dari contoh di atas, kita bisa melihat bahwa memanggil fungsi anonim cukup merepotkan karena kita harus mengetik definisi fungsinya lalu memberi argumen. Bisa dibayangkan jika kita ingin menggunakan fungsi tersebut secara berulang maka kita harus mengetik ulang definisinya. Maka dari itu, fungsi anonim lebih cocok dipakai untuk komputasi yang sederhana dan jarang digunakan.

## Menamai fungsi 

Untuk membuat fungsi bernama agar bisa dipanggil secara berulang, ada dua cara yang bisa dilakukan: gunakan (1) `def` binding atau (2) `defn`.

### def binding

Dengan `def` *binding*, kita bisa memberi nama kepada fungsi anonim yang kita buat. Seperti yang sudah dilihat, fungsi `fn` ataupun literal fungsi `#(...)` mengembalikan object fungsi. Object fungsi yang dikembalikan ini bisa kita `bind` ke suatu symbol (atau nama) yang kita inginkan. Dalam hal ini, object fungsi bisa diperlakukan layaknya nilai biasa dan dapat di-*bind* seperti ekspresi `x = 42`. Masih dengan contoh fungsi yang sama seperti di atas, mari kita namai mereka.

```clojure 
user> (def fungsi-tanpa-parameter
        (fn [] 
          (println "tanpa parameter")))
;; => #'user/fungsi-tanpa-parameter

user> (def fungsi-tanpa-parameter-literal
        #(println "tanpa parameter"))
;; => #'user/fungsi-tanpa-parameter-literal

user> (def kuadrat
        (fn [x]
          (* x x)))
;; => #'user/kuadrat

user> (def kuadrat-literal
        #(* % %))
;; => #'user/kuadrat-literal

user> (def jumlah
        (fn [x y]
          (+ x y)))
;; => #'user/jumlah

user> (def jumlah-literal
        #(+ %1 %2))
;; => #'user/jumlah-literal
```
Setelah fungsi anonim di-*bind* dengan symbol, mari kita panggil mereka dalam *function call*.

```clojure 
user> (fungsi-tanpa-parameter)
;; tanpa parameter
;; => nil
user> (fungsi-tanpa-parameter-literal)
;; tanpa parameter
;; => nil
user> (kuadrat 6)
;; => 36
user> (kuadrat-literal 7)
;; => 49
user> (jumlah 42 35)
;; => 77
user> (jumlah-literal 33 55)
;; => 88
```
Jika dibandingkan dengan *function call* pada fungsi anonim, jadi lebih sederhana, kan? Selain itu, kita bisa memanggil fungsi berulang kali tanpa perlu mengetik ulang definisi fungsinya. 

Secara struktur, *function call* `(kuadrat 5)` dan `((fn [x] (* x x)) 5)` itu sama. Namun, kita menyembunyikan definisi fungsi dan menggantinya dengan sebuah symbol/nama. Inilah salah satu contoh sederhana dari proses **abstraksi**. Proses abstraksi kurang lebih berarti menyembunyikan detail yang kurang relevan untuk mengurangi kompleksitas. Dalam contoh di atas, kita menyembunyikan definisi fungsi anonim dan memberinya nama sehingga *function call* kita terlihat lebih "bersih".

Layaknya tipe data lain seperti integer dan string, fungsi juga memiliki fungsi predikat `fn?`. Fungsi predikat ini bisa digunakan untuk memeriksa apakah suatu symbol adalah fungsi atau bukan. Berikut adalah contoh fungsi predikat `fn?`.

```clojure 
user> (fn? fungsi-tanpa-parameter)
;; => true
user> (fn? fungsi-tanpa-parameter-literal)
;; => true
user> (fn? kuadrat)
;; => true
user> (fn? println)
;; => true
user> (fn? +)
;; => true
user> (fn? 123)
;; => false
```

### Membuat fungsi dengan defn

Fungsi `defn` adalah cara utama untuk membuat fungsi di Clojure. Dengan `def` (tanpa `n`), kita hanya bisa melakukan *binding* object fungsi ke suatu symbol. Namun, jika kita ingin menambahkan dokumentasi/docstring atau metadata lain di dalam fungsi, `def` tidak mendukungnya dan harus memakai `defn`. Singkatnya, `defn` adalah versi `def` khusus untuk fungsi.

Berikut adalah sintaksis fungsi `defn`:
```clojure 
(defn nama-fungsi doc-string? attr-map? [parameter*] prepost-map? badan-fungsi)
```

Dari sintaksis `defn` di atas, kita bisa melihat kemiripannya dengan sintaksis `def`. Namun ada beberapa hal yang baru seperti `doc-string?`, `attr-map?`, dan `prepost-map?`.

> Untuk saat ini mohon abaikan `attr-map?` dan `prepost-map?`.

Jika kita ubah contoh-contoh fungsi sebelumnya dengan menggunakan `defn`, maka strukturnya menjadi seperti berikut.

```clojure 
user> (defn fungsi-tanpa-parameter
        "Ini adalah dokumentasi untuk pembuatan 
         fungsi tanpa parameter dengan defn.
         Docstring bersifat opsional."
        [] ;; vector untuk parameter
        (println "tanpa parameter dengan defn"))
;; => #'user/fungsi-tanpa-parameter

user> (defn kuadrat
        "Fungsi untuk menghitung nilai kuadrat"
        [x] ;; vector untuk parameter
        (* x x))
;; => #'user/kuadrat

;; contoh tanpa docstring dan dibuat dalam satu baris
user> (defn jumlah [x y] (+ x y))
;; => #'user/jumlah

;; contoh function call. 
;; masih sama seperti sebelumnya.
user> (fungsi-tanpa-parameter)
;; tanpa parameter dengan defn
;; => nil
user> (kuadrat 10)
;; => 100
user> (jumlah 6 7)
;; => 13

;; memeriksa docstring
user> (doc fungsi-tanpa-parameter)
;; -------------------------
;; user/fungsi-tanpa-parameter
;; ([])
;;   Ini adalah dokumentasi untuk pembuatan 
;;   fungsi tanpa parameter dengan defn
;; => nil
user> (doc kuadrat)
;; -------------------------
;; user/kuadrat
;; ([x])
;;   Fungsi untuk menghitung nilai kuadrat
;; => nil
user> (doc jumlah)
;; -------------------------
;; user/jumlah
;; ([x y])
;; => nil
```

Fungsi `defn` memudahkan kita untuk mendefinisikan fungsi dan sangat diutamakan saat kita membuat program Clojure. Membuat fungsi dengan `def` hanya cocok dalam beberapa kasus saja dan cukup jarang ditemui di *real-world application*.

## Penutup

Di bagian ini kita sudah belajar cara membuat fungsi anonim dan fungsi bernama dengan `fn`, `def`, dan `defn`. Fungsi anonim cocok dipakai untuk operasi sederhana dan hanya dilakukan sekali saja. Bila ingin mendefinisikan fungsi yang kompleks beserta dokumentasi dan metadata lainnya, sangat disarankan untuk memakai `defn`.

Di bagian selanjutnya kita masih akan belajar tentang fungsi dan berkenalan dengan beberapa hal yang akan membantu kita dalam membuat fungsi seperti destrukturisasi parameter, *multi-arity*, dsb.
