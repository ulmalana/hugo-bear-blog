+++
title = "Bagian 6: Struktur Data Koleksi"
date = "2023-01-28T18:39:28+07:00"

#
# description is optional
#
description = "Mengenal struktur data koleksi Clojure seperti list, vector, set, dan map."

tags = ["clojure","tutorial",]
+++

Kita sudah mengenal tipe data dasar Clojure di [bagian 5](https://riz.maulana.me/blog/2023/01/bagian-5-tipe-data-dasar/). Sekarang kita akan belajar struktur data koleksi yang bisa digunakan untuk menghimpun tipe-tipe data dasar tersebut untuk memecahkan masalah yang lebih kompleks. Ada beberapa struktur data koleksi (setelah ini akan disebut sebagai koleksi saja) di Clojure, antara lain list, vector, set, dan map. Keempat struktur data tersebut mempunyai abstraksi yang sama: **sequence**. Maksud dari abstraksi sequence adalah keempat koleksi tersebut sama-sama "sesuatu yang dirangkai". Jadi, terlepas dari perbedaan implementasi dan fungsionalitas tiap koleksi, mereka punya karakteristik dan operasi dasar yang sama. Selain itu, tiap koleksi bersifat heterogen, yang berarti mereka bisa menyimpan elemen dengan bermacam-macam tipe data dalam satu koleksi. Di bagian ini, kita akan membahas implementasi konkret dari tiap koleksi (list, vector, set, dan map) dahulu sebelum masuk ke abstraksi sequence. Di tiap koleksi, saya akan memberikan contoh operasi dasar yang bisa dilakukan terhadap mereka.

## Immutability dan Persistence
Sebelum melangkah lebih jauh, ada yang perlu diingat lagi tentang data di Clojure, yaitu mereka bersifat **immutable**, yang berarti kita tidak bisa mengubah data secara sembarangan. Selain itu, mereka juga bersifat **persistent**, yang berarti nilai lama dari suatu data akan tetap disimpan sebagai bagian dari *historical records* atau catatan historis. 

Mari kita buat sebuah analogi agar lebih mudah dipahami. Misal kita ingin menyimpan data tinggi suatu pohon dalam meter. Di bahasa pemrograman lain tanpa *immutability*, sebuah nilai disimpan dalam sebuah **boks** yang kita beri nama `Tinggi` agar mudah diakses. Jika kita ingin mengubah nilai `Tinggi`, nilai di dalam boks tersebut harus diganti dengan nilai baru. Ini yang dinamakan *mutable data*; data yang ada di satu boks tersebut bisa diganti-ganti. Data yang sudah diganti akan hilang dan tidak ada catatan historis dari data kita. Selain itu, nama `Tinggi` sudah **menempel** dengan boks, atau dengan kata lain, identitas/nama dan nilai adalah satu kesatuan.

Clojure punya model yang berbeda. Identitas/nama dan nilai dipisah, yang berarti nama `Tinggi` **menunjuk** (bukan menempel seperti contoh di atas) ke lokasi memori yang menyimpan nilai. Nama `Tinggi` ini akan menunjuk ke lokasi memori lain jika nilainya perlu diperbarui. Atau kalau pakai istilah Clojure, kita **rebind** (mengikat/mengaitkan/mengasosiasikan ulang) identitas/nama ke lokasi yang baru. Dengan begini, apabila ingin mengganti nilai `Tinggi`, proses yang terjadi kurang lebih seperti di bawah:

1. Data di lokasi awal dibiarkan.
2. Data pengganti akan disimpan di lokasi yang baru.
3. Nama `Tinggi` menunjuk ke lokasi baru tempat disimpannya data pengganti.

![1-immutability](/img/tutorial-clojure/bab-6/immutability-ed.png)

Contoh yang cukup sederhana untuk menggambarkan model *immutability* dan *persistence* di Clojure adalah tinggi badan manusia. Di sini, yang menjadi identitas adalah "tinggi badan" dan ia tidak berubah. Namun, nilai dari "tinggi badan" (atau nilai yang ditunjuknya) berubah-ubah. Pada saat kecil, tinggi badan kita mungkin 80 cm, dan saat dewasa tinggi badan kita bertambah menjadi 160 cm misalnya. Jadi, meskipun saat ini tinggi kita adalah 160 cm, bukan berarti kita bisa mengabaikan tinggi badan kita sebelumnya. Saat masih kecil, 80 cm itu adalah tinggi badan yang sebenarnya dan valid *pada saat itu*. Dengan begini, kita punya semacam "catatan historis" dari data kita. Tidak usah khawatir kalau konsep *immutability* dan *persistence* ini cukup susah dipahami, terutama di implementasinya. Intinya adalah kalau kita ingin mengubah nilai suatu variabel, Clojure akan membuat nilai baru dan jika diperlukan menyuruh nama variabel menunjuk ke yang baru ini. Mayoritas operasi/fungsi Clojure untuk koleksi akan mengembalikan koleksi baru.



Dalam konteks koleksi ini, wajar kalau kita khawatir dengan penggunaan memori karena tiap perubahan akan memakan memori. Tidak perlu khawatir karena Clojure sudah dioptimasi agar lebih efisien dalam penggunaan memori dan punya teknik **structural sharing**. Seperti namanya, *structural sharing* berarti struktur data baik yang lama maupun yang baru dapat berbagi lokasi memori yang sama. Misalkan kita membuat list `A` yang berisi `(10 11 12)`, lalu membuat list `B` dengan menambahkan satu elemen lagi ke list `A`. Maka, *structural sharing* yang terjadi kurang lebih seperti di gambar berikut:

![2-sharing](/img/tutorial-clojure/bab-6/structural-sharing.png)

Oke, setelah sedikit penjelasan tentang *immutability* dan *persistence*, mari kita mulai berkenalan dengan koleksi yang ada di Clojure. Jangan lupa untuk membuka REPL kalau ingin mengikuti tutorial ini.


## List

Struktur data pertama yang akan kita pelajari adalah list. List adalah struktur data utama di Clojure (serta Lisp pada umumnya) dan dipakai untuk mengumpulkan berbagai macam tipe data dalam satu koleksi. Jadi, ia bersifat heterogen, bukan homogen (tidak masalah kalau ingin menyimpan data homogen).

Di belakang layar, list diimplementasikan sebagai *singly linked-list*. *Singly linked-list* ini dibuat menggunakan **cons cell**, yang terdiri dari 2 slot, slot kanan dan slot kiri seperti pada gambar di bawah.

![3-cons-cell](/img/tutorial-clojure/bab-6/cons-cell-ed.png)

Kedua slot tersebut bisa digunakan untuk menyimpan sebuah nilai atau menunjuk ke cons cell lain. Sebagai tanda "stop" dari list, cons cell paling akhir harus menunjuk ke `nil` atau list kosong `'()`. Berikut adalah contoh list dengan satu elemen, yaitu `'(1)`. 

![4-cons-1](/img/tutorial-clojure/bab-6/cons1-ed.png)

Masih terkait dengan cons cell, kita bisa membangun list yang lebih panjang dengan fungsi `cons`. `cons` berasal dari kata *construct* yang bisa diartikan sebagai "membangun" atau "membuat". Dalam konteks Clojure dan Lisp, `cons` dapat digunakan untuk membuat rangkaian data dalam list. Ia dipakai untuk membuat cons cell baru dan menggabungkannya dengan cons cell lain. Fungsi `cons` memiliki sintaksis sebagai berikut:

```clojure 
(cons <elemen> <koleksi>)
```

Karena diimplementasikan sebagai *singly linked-list*, operasi penambahan elemen paling efisien adalah penambahan di depan/kepala. Jika kita ingin menyisipkan/mengubah nilai di tengah/paling akhir atau secara acak, prosesnya tidak efisien karena harus mengakses banyak cons cell. Maka dari itu, fungsi `cons` akan menambahkan elemen di paling depan. 
Berikut adalah contoh membuat list `'(1)` dan `'(1 2 3)` menggunakan fungsi `cons`:

```clojure 
user> (cons 1 nil)
;; => (1)
user> (cons 1 ()) ;; list kosong bisa tanpa tanda petik '
;; => (1)
user> (cons 1 '())
;; => (1)
user> (cons 1 2) ;; argumen kedua harus dalam koleksi atau nil
;; Execution error (IllegalArgumentException) at user/eval7534 (REPL:48).
;; Don't know how to create ISeq from: java.lang.Long
user> (cons 1 '(2))
;; => (1 2)
user> (cons 1 '(2 3))
;; => (1 2 3)
user> (cons 1 (cons 2 (cons 3 nil)))
;; => (1 2 3)
user> (cons '(1 2) '(3 4)) ;; '(1 2) dianggap sebagai satu elemen
;; => ((1 2) 3 4)
user> (cons 0 (cons 1 (cons 2 (cons 3 nil))))
;; => (0 1 2 3)
```

**Representasi list `'(0 1 2 3)` di cons cell**:

![5-list](/img/tutorial-clojure/bab-6/list-ed.png)

Seringkali kita tidak perlu memakai `cons` untuk membuat list karena kita bisa memakai literal list secara langsung seperti `'(1 2 3)` atau dengan fungsi `list` seperti `(list 1 2 3)`. Perlu diingat lagi bahwa list juga bisa dianggap sebagai *function call*, sehingga kita harus memberikan tanda petik satu `'` untuk mendapatkan datanya. Fungsi `cons` berguna apabila kita ingin menambah elemen satu per satu ke list yang sudah ada. 

```clojure 
;; simpan list dalam my-list agar dapat dipakai berulang kali.
;; my-list dapat menyimpan tipe data yang bervariasi.
user> (def my-list '("satu" 2 3.0 :empat \5 enam))
;; => #'user/my-list
user> my-list
;; => ("satu" 2 3.0 :empat \5 enam)
user> (def my-list-2 (list "satu" 2 3.0 :empat \5 'enam))
;; => #'user/my-list-2
user> my-list-2
;; => ("satu" 2 3.0 :empat \5 enam)
user> (cons 0 my-list)                  ;; mengembalikan list baru
;; => (0 "satu" 2 3.0 :empat \5 enam)
user> (cons nil my-list)
;; => (nil "satu" 2 3.0 :empat \5 enam)
user> my-list                           ;; my-list tidak berubah
;; => ("satu" 2 3.0 :empat \5 enam)
```

Alternatif fungsi `cons` adalah fungsi `conj` (kependekan dari *conjoin* yang berarti menggabungkan). Fungsi `conj` akan menambahkan elemen secara efisien tergantung dari struktur data yang dipakai. Dalam hal ini, karena list paling efisien kalau elemen ditambahkan di depan, `conj` akan bekerja seperti `cons`. Nanti kita akan melihat kalau `conj` bekerja dengan cara yang berbeda di koleksi lain. Selain itu, `cons` hanya bisa menambahkan satu elemen, sedangkan `conj` bisa menambahkan banyak elemen sekaligus. 

Perlu diperhatikan bahwa urutan argumen di `conj` berbeda dengan `cons`:
```clojure 
(conj <koleksi> <elemen>)
```

> **Tips**
>
> Untuk memeriksa dokumentasi dari tiap fungsi, kita bisa memakai fungsi `doc`. Selain itu kita juga bisa melihat *source code* dari fungsi clojure dengan fungsi `source`. Berikut adalah contoh jika kita ingin tahu dokumentasi serta *source code* dari fungsi `conj`. Output dari kedua fungsi tersebut tidak akan dijelaskan di bagian ini.
>
> ```clojure
> user> (doc conj)
> -------------------------
> clojure.core/conj
>  ([] [coll] [coll x] [coll x & xs])
>    conj[oin]. Returns a new collection with the xs
>      'added'. (conj nil item) returns (item).
>      (conj coll) returns coll. (conj) returns [].
>      The 'addition' may happen at different 'places' depending
>      on the concrete type.
>  nil
>  
>  user> (source conj)
>  (def
>   ^{:arglists '([] [coll] [coll x] [coll x & xs])
>     :doc "conj[oin]. Returns a new collection with the xs
>     'added'. (conj nil item) returns (item).
>     (conj coll) returns coll. (conj) returns [].
>     The 'addition' may happen at different 'places' depending
>     on the concrete type."
>     :added "1.0"
>     :static true}
>    conj (fn ^:static conj
>         ([] [])
>         ([coll] coll)
>         ([coll x] (clojure.lang.RT/conj coll x))
>         ([coll x & xs]
>          (if xs
>            (recur (clojure.lang.RT/conj coll x) (first xs) (next xs))
>            (clojure.lang.RT/conj coll x)))))
>  nil
> ```


```clojure 
user> (conj my-list 0) ;; perhatikan urutan argumen
;; => (0 "satu" 2 3.0 :empat \5 'enam)
user> (conj my-list 77 88 99 100)
;; => (100 99 88 77 "satu" 2 3.0 :empat \5 'enam)
user> (conj my-list '(77 88 99 100)) ;; '(77 88 99 100) dianggap satu elemen
;; => ((77 88 99 100) "satu" 2 3.0 :empat \5 'enam)

```

Kita bisa memeriksa apakah suatu koleksi bertipe list dengan fungsi predikat `list?`. Selain itu, untuk memeriksa apakah suatu list memiliki elemen atau kosong, pakai fungsi `empty?`.

```clojure 
user> (list? nil) ;; nil bukan list
;; => false
user> (list? ())
;; => true
user> (list? '())
;; => true
user> (list? my-list)
;; => true
user> (empty? nil) ;; tapi nil dianggap sebagai sesuatu yang kosong seperti list kosong.
;; => true
user> (empty? '())
;; => true
user> (empty? my-list)
;; => false
```

> fungsi `empty?` bisa dipakai di koleksi lain.


Indeks elemen list dimulai dari 0, sama seperti bahasa pemrograman lain. Untuk mengakses elemen dari suatu list berdasarkan indeksnya, kita bisa memakai fungsi `nth` dengan sintaksis:

```clojure 
(nth <koleksi> <indeks>)
```

Jika kita mencoba akses indeks yang tidak ada, `nth` akan error. `nth` punya argumen opsional untuk memberi tahu kita kalau elemen tidak ditemukan daripada menghasilkan *exception*.

```clojure 
user> (nth my-list 0)
;; => "satu"
user> (nth my-list 3)
;; => :empat
user> (nth my-list 10)
;; Execution error (IndexOutOfBoundsException) at user/eval7604 (REPL:144).
;; null
user> (nth my-list 10 :nilai-tidak-ada)
;; => :nilai-tidak-ada

```

Kita bisa mengambil nilai pertama dari suatu list dengan fungsi `first`. Kalau ingin mengambil nilai pertama dari list kosong, `first` akan mengembalikan `nil`. Pelengkap dari fungsi `first` ini adalah `rest`, yang mana akan mengembalikan list tanpa nilai pertamanya.

```clojure 
user> (first my-list)
;; => "satu"
user> (rest my-list)
;; => (2 3.0 :empat \5 'enam)
user> (first nil)
;; => nil
user> (first '())
;; => nil
user> (rest '())
;; => ()
user> (rest nil)
;; => ()
```

## Vector
Karena list tidak efisien saat mengakses elemen secara acak, Clojure menyediakan koleksi alternatif yang lebih sering dipakai dibandingkan list, yaitu vector. Vector mirip seperti array dan elemennya bisa diakses secara random dengan efisien. Tiap elemen di vector juga punya indeks dari 0.

Untuk membuat vector, kita bisa memakai literal dengan tanda kurung siku `[]` atau fungsi `vector`. Kita bisa juga  mengonversi koleksi lain ke vector dengan fungsi `vec`. Fungsi predikat vector adalah `vector?`.

```clojure 
user> (def my-vector ["satu" 2 3.0 :empat \5 'enam])
;; => #'user/my-vector
user> my-vector
;; => ["satu" 2 3.0 :empat \5 enam]
user> (def my-vector (vector "satu" 2 3.0 :empat \5 'enam))
;; => #'user/my-vector
user> (def my-vector-2 (vector "satu" 2 3.0 :empat \5 'enam))
;; => #'user/my-vector-2
user> my-vector-2
;; => ["satu" 2 3.0 :empat \5 enam]
user> (vec '(4 5 6))
;; => [4 5 6]
user> (vector? (vec '(4 5 6)))
;; => true
user> (vector? '(4 5 6))
;; => false
```

Fungsi `cons` di vector akan menambah elemen ke bagian depan, namun yang dikembalikan **bukan dalam vector** melainkan sequence. Sequence ini adalah logical list dan akan dibahas di akhir bagian ini. 

```clojure 
user> (cons 0 my-vector)
;; => (0 "satu" 2 3.0 :empat \5 enam) ;; bukan list, tapi sequence
user> (vector? (cons 0 my-vector))
;; => false
user> (list? (cons 0 my-vector))
;; => false
user> (seq? (cons 0 my-vector))
;; => true
```

Sementara itu, fungsi `conj` akan menambah elemen ke bagian belakang vector. Ini karena vector mirip seperti array dan operasi penambahan elemen paling efisien adalah di paling belakang.

```clojure 
user> (conj my-vector 0)
;; => ["satu" 2 3.0 :empat \5 enam 0]
user> (conj my-vector nil)
;; => ["satu" 2 3.0 :empat \5 enam nil]
user> (conj my-vector 7)
;; => ["satu" 2 3.0 :empat \5 enam 7]
user> (conj my-vector 33 44 55)
;; => ["satu" 2 3.0 :empat \5 enam 33 44 55]
user> (conj my-vector '(33 44 55)) ;; bisa menambahkan list ke vector
;; => ["satu" 2 3.0 :empat \5 enam (33 44 55)]
user> (conj my-vector [33 44 55])
;; => ["satu" 2 3.0 :empat \5 enam [33 44 55]]
```

Untuk mengakses elemen vector berdasarkan indeksnya kita bisa memakai `nth` atau `get`. Perlu diperhatikan kalau `get` tidak dapat bekerja di list. Ini karena `get` bekerja untuk struktur data yang punya karakteristik *key-value pair* (*key-value pair* akan dijelaskan nanti). Vector dianggap sebagai *key-value pair* karena indeks vector diperlakukan menjadi *key* dan nilai di indeks tersebut menjadi *value*-nya. List tidak bisa bekerja seperti ini maka dari itu fungsi `get` tidak bisa diterapkan di list. `get` juga menerima argumen opsional yang akan dikembalikan jika tidak ada indeks yang dicari.

```clojure 
user> (nth my-vector 0)
;; => "satu"
user> (nth my-vector 5)
;; => enam
user> (get my-vector 0)
;; => "satu"
user> (get my-vector 4)
;; => \5
user> (get my-vector 99 :tidak-ada)
;; => :tidak-ada
```

Kita bisa memeriksa apakah suatu vector memiliki elemen di indeks tertentu dengan fungsi `contains?`. Fungsi tersebut akan **mengecek indeks**, bukan nilai di indeks yang dimaksud. `contains?` juga tidak bisa dipakai di list.

```clojure 
user> (contains? my-vector 0) ;; my-vector punya nilai di indeks 0?
;; => true
user> (contains? my-vector 4) 
;; => true
user> (contains? my-vector 99) ;; my-vector punya nilai di indeks 99?
;; => false
user> (contains? my-list 0)
;; Execution error (IllegalArgumentException) at user/eval7662 (REPL:220).
;; contains? not supported on type: clojure.lang.PersistentList
```

Untuk mengubah elemen suatu vector, fungsi `assoc` (kependekan dari *associate*) dapat digunakan. Selain itu, `assoc` juga bisa digunakan untuk menambah elemen, namun terbatas di indeks `n+1` di mana `n` adalah indeks terbesar saat ini. 

Fungsi `assoc` memiliki sintaksis sebagai berikut:

```clojure 
(assoc <koleksi> <indeks> <nilai>)
```

```clojure 
user> (assoc my-vector 2 :DIGANTI)
;; => ["satu" 2 :DIGANTI :empat \5 enam]
user> (assoc my-vector 7 :DIGANTI) ;; indeks terbesar adalah 5
;; Execution error (IndexOutOfBoundsException) at user/eval7668 (REPL:229).
;; null
user> (assoc my-vector 6 :DIGANTI)
;; => ["satu" 2 3.0 :empat \5 enam :DIGANTI]
```

Seringkali kita menemui kasus di mana yang kita butuhkan hanya sebagian elemen saja, bukan keseluruhan vector. Vector parsial ini disebut sebagai subvector dan cara mendapatkannya bisa dengan fungsi `subvec`. Fungsi `subvec` akan memotong vector berdasarkan indeks yang kita masukkan sebagai titik awal subvector (dan titik akhir jika diperlukan).

```clojure 
user> (subvec my-vector 3) ;; potong mulai dari indeks 3
;; => [:empat \5 enam]
user> (subvec my-vector 2 4) ;; 
;; => [3.0 :empat]
```

## Set
Set adalah koleksi dari nilai-nilai yang unik. Jika dalam suatu set terdapat duplikasi nilai, maka hanya ada satu nilai yang disimpan. Ini sama seperti konsep himpunan di matematika. Literal set menggunakan `#{}`, contohnya adalah `#{1 2 3}`. Kita bisa membuat set dengan fungsi `hash-set` atau `sorted-set`. Perbedaan dari kedua fungsi tersebut adalah `sorted-set` dapat mengurutkan nilai dalam set. Fungsi predikatnya adalah `set?`.

```clojure 
;; literal set harus tanpa duplikasi, jika tidak akan error.
user> (def my-set #{1 2 1 3 4 5 5})
;; Syntax error reading source at (REPL:107:35).
;; Duplicate key: 1
user> (def my-set #{1 2 3 4 5}) 
;; => #'user/my-set
user> my-set
;; => #{1 4 3 2 5} ;; urutan seringkali tidak sesuai dengan yang kita masukkan di awal
user> (hash-set 1 2 3 4 2 5 1) ;; duplikat akan diabaikan
;; => #{1 4 3 2 5}
user> (sorted-set 4 2 4 5 3 1 2)
;; => #{1 2 3 4 5}
user>  (set? my-set)
;; => true
user>  (set? my-list)
;; => false
```

Kita bisa mengonversi struktur koleksi lain ke set dengan fungsi `set`. Jika di koleksi lain ada duplikasi, nilai duplikat juga akan diabaikan.

```clojure 
user> (set my-vector)
;; => #{3.0 enam "satu" 2 \5 :empat}
user> (set my-list)
;; => #{3.0 'enam "satu" 2 \5 :empat}
user> (set [1 2 3 1 2 3])
;; => #{1 3 2}
user> (set '(1 2 3 1 2 3))
;; => #{1 3 2}
```

Berbeda dengan fungsi `contains?` di vector yang memeriksa indeks, fungsi `contains?` di set benar-benar memeriksa apakah suatu nilai ada di set.

```clojure 
user> (contains? my-set 3)
;; => true
user> (contains? my-set 0)
;; => false
user> (contains? my-set 99)
;; => false
```

Fungsi `get` di set bisa dipakai untuk mendapatkan nilai yang ada di set. Set bisa dianggap sebagai *key-value pair*, di mana elemen dalam set dianggap sebagai *key* **sekaligus** *value*. Sehingga, nilai kembaliannya adalah elemen itu sendiri (jika ada di set).

```clojure 
user> (get my-set 0)
;; => nil
user> (get my-set 1)
;; => 1
user> (get my-set 4)
;; => 4
user> (get my-set 42)
;; => nil
```

Karena set berisi nilai yang unik, operasi pembaruan yang mungkin dilakukan adalah antara menambah elemen baru (yang unik) atau menghapus elemen yang sudah ada. 

Untuk menambah elemen pada set, gunakan fungsi `conj`.

```clojure 
user> (conj my-set 42)
;; => #{1 4 3 2 5 42}
user> (conj my-set 88 77 66 66)
;; => #{1 88 4 77 3 2 66 5}
user> (conj my-set 42 42 84 nil)
;; => #{nil 1 4 3 2 5 42 84}

```
Fungsi `disj` (yang merupakan kependekan dari *disjoin* yang berarti memisahkan) dapat dipakai untuk menghapus elemen dari set.

```clojure 
user> (disj my-set 4)
;; => #{1 3 2 5}
user> (disj my-set 1 2 4 9) 
;; => #{3 5}
user> (disj my-set 29)
;; => #{1 4 3 2 5}
```

## Map
Map adalah struktur data koleksi untuk menyimpan nilai dalam bentuk *key-value pair*. *Pair* ini digunakan untuk mengasosiasikan 2 nilai, contohnya asosiasi nama orang dan tinggi badan mereka. Dalam Bahasa Indonesia, map lebih tepat diterjemahkan sebagai pemetaan, dalam hal ini pemetaan satu nilai ke nilai yang lain (seperti contoh asosiasi di atas). Map sangat sering digunakan di Clojure karena ia memudahkan kita untuk memodelkan masalah yang kita selesaikan. Nantinya kita juga akan sering bertemu dengannya. Literal map memakai kurung kurawal `{}` (perhatikan perbedaannya dengan set yang punya `#` di depannya). Contoh sederhana map adalah `{"ani" 167}` yang mengasosiasikan nilai `"ani"` dengan nilai `167`. *key* dalam suatu map (`"ani"` untuk map di atas) bisa dalam semua tipe data tapi harus unik. Fungsi `hash-map` dan `sorted-map` juga dapat dipakai untuk membuat map. Seperti pada set, perbedaan kedua fungsi tersebut adalah `sorted-map` bisa mengembalikan map yang sudah terurut.

```clojure 
;; koma opsional tapi sangat membantu untuk membedakan tiap asosiasi
user> (def tinggi-badan {"ani" 167, "budi" 163, "catur" 172}) 
;; => #'user/tinggi-badan
user> tinggi-badan
;; => {"ani" 167, "budi" 163, "catur" 172}
user> (hash-map "budi" 163 "catur" 172 "ani" 167)
;; => {"catur" 172, "budi" 163, "ani" 167}
user> (sorted-map "budi" 163 "catur" 172 "ani" 167)
;; => {"ani" 167, "budi" 163, "catur" 172}
```

Fungs predikat untuk map adalah `map?`. Perlu saya informasikan juga bahwa ada fungsi `map` di Clojure. Kalau melihat dari pola di koleksi sebelumnya, fungsi ini seharusnya untuk mengonversi koleksi lain ke map. Sayangnya tidak seperti itu. Fungsi `map` adalah untuk **memetakan suatu fungsi ke tiap elemen suatu koleksi**. Kita akan bertemu fungsi `map` saat kita membahas pemrograman fungsional. 

```clojure 
user> (map? my-set)
;; => false
user> (map? tinggi-badan-key)
;; => true
user> (doc map)
;; -------------------------
;; clojure.core/map
;; ([f] [f coll] [f c1 c2] [f c1 c2 c3] [f c1 c2 c3 & colls])
;;   Returns a lazy sequence consisting of the result of applying f to
;;   the set of first items of each coll, followed by applying f to the
;;   set of second items in each coll, until any one of the colls is
;;   exhausted.  Any remaining items in other colls are ignored. Function
;;   f should accept number-of-colls arguments. Returns a transducer when
;;   no collection is provided.
;; nil
```


Di clojure, keyword sangat umum dipakai sebagai *key* sebuah map. Oleh karena itu, contoh di atas jika memakai keyword akan menjadi:

```clojure 
user> (def tinggi-badan-key {:ani 167, :budi 163, :catur 172})
;; => #'user/tinggi-badan-key
user> tinggi-badan-key
;; => {:ani 167, :budi 163, :catur 172}

```

Untuk mendapatkan nilai yang terasosiasi dengan suatu *key* bisa memakai `get`. Keyword itu sendiri juga bisa berperilaku seperti sebuah operator atau fungsi. Contoh di bawah ini bisa menjelaskan apa yang saya maksud dengan keyword sebagai operator/fungsi.

```clojure 
user> (get tinggi-badan "catur")
;; => 172
user> (get tinggi-badan-key :budi)
;; => 163
user> (get tinggi-badan-key :dina)
;; => nil
user> (get tinggi-badan-key :dina :tidak-ada)
;; => :tidak-ada
user> (:ani tinggi-badan-key) ;; keyword sebagai fungsi
;; => 167
```

Kadang kita ingin mendapatkan *key* saja atau nilainya saja yang ada di map. Fungsi `keys` dan `vals` bisa membantu untuk mendapatkannya.

```clojure 
user> (vals tinggi-badan-key)
;; => (167 163 172)            ;; sequence, bukan list
user> (keys tinggi-badan-key)
;; => (:ani :budi :catur)      ;; sequence, bukan list
user> (keys tinggi-badan)
;; => ("ani" "budi" "catur")   ;; sequence, bukan list
user> (list? (keys tinggi-badan-key))
;; => false
user> (seq? (keys tinggi-badan-key))
;; => true
```

Kita bisa menambahkan *key-value pair* baru dalam map dengan fungsi `assoc`. Selain itu, ia juga bisa untuk mengganti nilai yang sudah ada di map.

```clojure 
user> (assoc tinggi-badan-key :dina 164)
;; => {:ani 167, :budi 163, :catur 172, :dina 164}
user> (assoc tinggi-badan-key :ani 169)
;; => {:ani 169, :budi 163, :catur 172}
```

Dengan fungsi `update`, kita bisa memperbarui nilai berdasarkan dengan fungsi lain. Misalnya tinggi Budi bertambah 1 cm dan kita ingin memperbarui tinggi Budi di map. Fungsi `inc` (*increment*) akan menambah 1 ke argumennya, dan ia bisa dipakai untuk mengubah tinggi Budi dengan fungsi `update`.

```clojure 
user> (update tinggi-badan-key :budi inc)
;; => {:ani 167, :budi 164, :catur 172}
```

Selanjutnya, untuk menghapus *key-value pair* di map, gunakan fungsi `dissoc`. Fungsi `dissoc` (*dissociate*) adalah kebalikan dari fungsi `assoc`. Jika *key* yang ingin kita hapus tidak ada di map, maka ia akan diabaikan tanpa memunculkan error.

```clojure 
user> (dissoc tinggi-badan-key :catur :ani)
;; => {:budi 163}
user> (dissoc tinggi-badan-key :fina)
;; => {:ani 167, :budi 163, :catur 172}
```

## Abstraksi Sequence

Sekarang kita masuk ke pembahasan sequence. Sequence adalah abstraksi untuk sesuatu yang bisa dirangkai. List, vector, map, dan set adalah beberapa cara untuk merangkai dan mengumpulkan data. Dengan abstraksi sequence, kita bisa memperlakukan keempat struktur data tersebut sebagai sesuatu yang sama, yaitu sama-sama "rangkaian" data. 

Sequence ditampilkan mirip seperti list biasa dengan tanda kurung dan seringkali orang menyangka sequence adalah list, tapi sebenarnya ia berbeda dengan list. Sequence adalah **logical list** dan punya implementasi yang berbeda dengan list. List biasa diimplementasikan dalam cons cell, sedangkan sequence diimplementasikan dalam `ISeq` interface yang memungkinkan kita untuk mengakses elemen dari berbagai macam struktur data koleksi.


Di contoh-contoh sebelumnya kita sudah melihat ada beberapa fungsi yang mengembalikan sequence. Mayoritas fungsi Clojure yang berhubungan dengan koleksi memang akan mengonversi suatu koleksi menjadi sequence agar bisa diproses. Proses konversi ini terjadi karena kebanyakan algoritma dan fungsi di Clojure didefinisikan berdasarkan sequence. Setelah diproses, sequence dapat dikonversi lagi ke struktur data awal jika diperlukan.

Seperti koleksi lain, sequence juga bersifat *immutable* dan *persistent*. Kita bisa mengonversi suatu koleksi ke sequence dengan fungsi `seq`. Selain itu, kita juga bisa memeriksa apakah suatu koleksi dapat dikonversi menjadi sequence dengan fungsi `seqable?`. Karena list dan sequence ditampilkan dengan tanda kurung, fungsi predikat `seq?` dapat membantu kita untuk membedakan mereka.

```clojure 
user> (seq my-list)
;; => ("satu" 2 3.0 :empat \5 enam)
user> (seq my-vector)
;; => ("satu" 2 3.0 :empat \5 enam)
user> (seq my-set)
;; => (1 4 3 2 5)
user> (seq tinggi-badan-key)
;; => ([:ani 167] [:budi 163] [:catur 172])
user> (seqable? my-vector)
;; => true
user> (seqable? "test") ;; string dianggap sebagai rangkaian dari character
;; => true
user> (seqable? 3)
;; => false
user> (seqable? :tidak-bisa)
;; => false
user> (seq? my-list) ;; literal list dianggap sebagai sequence
;; => true
user> (seq? my-vector)
;; => false
user> (seq? (seq my-set))
;; => true
```

Sequence punya 3 operasi dasar yang bisa dipakai untuk semua struktur data koleksi yang menerapkan abstraksi sequence, yaitu `first`, `rest`, dan `cons` yang mana sudah kita temui di contoh-contoh sebelumnya. Kebanyakan fungsi untuk memroses koleksi didasarkan pada ketiga fungsi tersebut.

1. `first`

	Fungsi ini mengembalikan nilai pertama dari suatu koleksi. Jika koleksi tersebut kosong, `nil` akan dikembalikan.

    ```clojure 
    user> (first my-list)
    ;; => "satu"
    user> (first my-vector)
    ;; => "satu"
    user> (first my-set)
    ;; => 1
    user> (first tinggi-badan-key)
    ;; => [:ani 167]
    user> (first '())
    ;; => nil
    user> (first [])
    ;; => nil
    user> (first #{})
    ;; => nil
    user> (first {})
    ;; => nil
    ```

2. `rest`

	Fungsi ini adalah pelengkap dari `first`. Ia akan mengembalikan sequence tanpa nilai pertamanya. Dengan fungsi predikat yang sudah dipelajari, kita bisa memastikan bahwa yang dikembalikan adalah sequence, bukan list. Jika tidak ada nilai lagi dalam koleksi, ia akan mengembalikan `()`.

    ```clojure 
    user> (rest my-list)
    ;; => (2 3.0 :empat \5 enam)
    user> (rest my-vector)
    ;; => (2 3.0 :empat \5 enam)
    user> (rest my-set)
    ;; => (4 3 2 5)
    user> (rest tinggi-badan-key)
    ;; => ([:budi 163] [:catur 172])
    user> (rest '())
    ;; => ()
    user> (rest [])
    ;; => ()
    user> (rest #{})
    ;; => ()
    user> (rest {})
    ;; => ()
    user> (list? (rest my-vector))
    ;; => false
    user> (seq? (rest my-vector))
    ;; => true
    user> (type (rest my-vector))
    ;; => clojure.lang.PersistentVector$ChunkedSeq
    ```

3. `cons`

	Di atas saya sudah menjelaskan fungsi `cons` (beserta `first` dan `rest`) dalam konteks list. Saya juga sempat memberikan contoh fungsi `cons` untuk vector yang ternyata nilai kembaliannya adalah sequence, bukan vector. Jika kita buka dokumentasi fungsi `cons`, akan semakin jelas bahwa ia bekerja dalam konteks sequence.

    ```clojure 
    user> (doc cons)
    ;; -------------------------
    ;; clojure.core/cons
    ;; ([x seq])
    ;;  Returns a new seq where x is the first element and seq is
    ;;    the rest.
    ;; nil

    ```

	Fungsi `cons` dapat bekerja di banyak koleksi dan akan mengembalikan sequence.

    ```clojure 
    user> (cons 99 my-list)
    ;; => (99 "satu" 2 3.0 :empat \5 enam)
    user> (cons 99 my-vector)
    ;; => (99 "satu" 2 3.0 :empat \5 enam)
    user> (cons 99 my-set)
    ;; => (99 1 4 3 2 5)
    user> (cons 99 tinggi-badan-key) ;; karena sudah dalam sequence, kita bisa masukkan nilai tunggal.
    ;; => (99 [:ani 167] [:budi 163] [:catur 172])
    user> (cons [:dina 165] tinggi-badan-key)
    ;; => ([:dina 165] [:ani 167] [:budi 163] [:catur 172])
    user> (seq? (cons [:dina 165] tinggi-badan-key))
    ;; => true
    ```

Ada satu hal lagi yang ingin saya sampaikan terkait sequence, yaitu mereka ***lazy***. Maksudnya adalah Clojure tidak akan mengevaluasi sequence **sampai benar-benar dibutuhkan**. Dengan karakteristik ini, kita bisa membuat sebuah sequence dengan panjang tak terbatas (sampai *infinity*) tanpa harus menunggu Clojure mendapatkan semua elemennya (karena tidak perlu). Kita akan bertemu lagi dengan *lazy sequence* nanti. Untuk sekarang, itu saja yang perlu diketahui.

## Penutup

Di bagian yang cukup panjang ini, kita sudah belajar struktur data koleksi Clojure yang bisa digunakan untuk menghimpun data, mulai dari list, vector, set, lalu map. Kita juga sudah melihat bahwa Clojure punya abstraksi sequence yang memungkinkan kita untuk memperlakukan semua koleksi sebagai entitas yang sama, yaitu sequence. Di belakang layar, kebanyakan fungsi Clojure bekerja berdasarkan sequence. Selain itu, kita sudah mengenal 3 operasi dasar sequence yaitu `first`, `rest`, dan `cons`.

Di bagian selanjutnya, kita akan berkenalan dengan percabangan dan cara mengontrol alur kerja program Clojure kita.
