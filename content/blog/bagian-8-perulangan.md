+++
title = "Bagian 8: Perulangan"
date = "2023-02-15T16:06:46+07:00"

#
# description is optional
#
description = "Bagian 8: Mengenal perulangan di Clojure."

tags = ["clojure","tutorial",]
+++

Setelah belajar percabangan di [bagian 7](https://riz.maulana.me/blog/2023/02/bagian-7-percabangan/), kita akan belajar cara mengevaluasi ekspresi secara berulang di bagian 8 ini. Secara umum, ada 2 cara untuk melakukan perulangan di Clojure: iterasi biasa dan rekursi. Dengan iterasi biasa, kita bisa mengulang evaluasi berdasarkan jumlah perulangan yang diinginkan atau suatu koleksi. Padanan fungsi perulangan di bahasa pemrograman lain untuk iterasi biasa adalah `for` dan `while`. Perlu diketahui di awal bahwa fungsi `for` di Clojure punya perilaku yang berbeda dibanding bahasa pemrograman lain dan akan kita lihat nanti. Selain iterasi biasa, perulangan dengan rekursi juga sering digunakan di Clojure. Dengan rekursi, kita perlu menentukan *titik rekursi* yang akan dipakai sebagai tempat kembalinya proses evaluasi untuk melakukan perulangan. Jangan khawatir kalau penjelasan saya susah dipahami karena nanti akan ada contoh kode yang harapannya lebih mudah dipahami.

Kita akan mulai dengan iterasi biasa yang cara kerjanya mirip dengan perulangan di bahasa pemrograman. 

## dotimes
Seperti namanya, `dotimes` digunakan untuk perulangan sesuai dengan jumlah yang diinginkan. Jumlah perulangan yang diinginkan harus dalam bilangan bulat dan harus di-*bind* dengan symbol arbitrer. Symbol yang sudah di-*bind* ini dapat dipakai di dalam ekspresi yang diulang. `dotimes` mirip dengan perulangan `for` dari `0` sampai `n-1`, di mana `n` adalah jumlah perulangan yang diinginkan. Perulangan dengan `dotimes` biasanya digunakan untuk mengevaluasi ekspresi dengan *side effects* seperti mengirim data via Internet atau sekadar menampilkan informasi secara berulang. Jika di dalam `dotimes` tidak ada ekspresi dengan *side effects*, kita hanya akan melihat `nil` sebagai nilai kembalian `dotimes`.

Berikut adalah contoh perulangan dengan `dotimes`:

```clojure 
user> (dotimes [n 5]                       ;; bind 5 ke symbol n
        (str "n kuadrat adalah " (* n n))) ;; str adalah fungsi murni (tanpa side effects)
;; => nil   ;; nilai kembalian dotimes

user> (dotimes [n 5] 
        (println "perulangan ke:" n))      ;; println punya side effects
;; perulangan ke: 0
;; perulangan ke: 1
;; perulangan ke: 2
;; perulangan ke: 3
;; perulangan ke: 4
;; => nil             ;; nilai kembalian dotimes

user> (dotimes [n 10]
        (println "perulangan ke:" n)
        (println n "kuadrat adalah: " (* n n))
        (println "#####"))
;; perulangan ke: 0
;; 0 kuadrat adalah:  0
;; #####
;; perulangan ke: 1
;; 1 kuadrat adalah:  1
;; #####
;; perulangan ke: 2
;; 2 kuadrat adalah:  4
;; #####
;; perulangan ke: 3
;; 3 kuadrat adalah:  9
;; #####
;; perulangan ke: 4
;; 4 kuadrat adalah:  16
;; #####
;; perulangan ke: 5
;; 5 kuadrat adalah:  25
;; #####
;; perulangan ke: 6
;; 6 kuadrat adalah:  36
;; #####
;; perulangan ke: 7
;; 7 kuadrat adalah:  49
;; #####
;; perulangan ke: 8
;; 8 kuadrat adalah:  64
;; #####
;; perulangan ke: 9
;; 9 kuadrat adalah:  81
;; #####
;; => nil
```

*Binding* dalam `dotimes` bisa dalam bentuk float, tapi nantinya Clojure akan membulatkan ke bawah (*flooring*). Selain itu `dotimes` hanya mendukung 1 *binding*. Jika ingin melakukan *binding* lebih dari 1, kita harus membuat `dotimes` bersarang.

```clojure 
user> (dotimes [n 4.9] ;; dibulatkan ke bawah / flooring
        (println n))
;; 0
;; 1
;; 2
;; 3
;; => nil

user> (dotimes [i 3    ;; multi-binding akan error
                j 2]
        (println "i:" i "j:" j))
;; Syntax error macroexpanding dotimes at (*cider-repl ~:localhost:34233(clj)*:87:7).
;; dotimes requires exactly 2 forms in binding vector in user:87

user> (dotimes [i 3]
        (dotimes [j 2] ;; dotimes bersarang
          (println "i:" i "j:" j)))
;; i: 0 j: 0
;; i: 0 j: 1
;; i: 1 j: 0
;; i: 1 j: 1
;; i: 2 j: 0
;; i: 2 j: 1
;; => nil
```

Dalam beberapa kasus, kita tidak memerlukan nilai dalam *binding*. Selama ekspresi dievaluasi secara berulang sesuai dengan jumlah yang kita inginkan, itu sudah cukup. Untuk kasus tersebut, kita bisa mengganti symbol dalam *binding* dengan garis bawah `_`, yang berarti kita tidak memedulikan *binding* yang ada karena kita tidak butuh nilai dalam *binding* tersebut. Pada prakteknya, kita masih bisa mengakses nilai symbol `_` dalam *binding*. Garis bawah `_` bertindak sebagai *wildcard* dan merupakan konvensi saat kita ingin mengabaikan suatu nilai dalam *binding*.

```clojure 
user> (dotimes [_ 5] 
        (println "binding diabaikan"))
;; binding diabaikan
;; binding diabaikan
;; binding diabaikan
;; binding diabaikan
;; binding diabaikan
;; => nil

user> (dotimes [_ 5]
        (println "binding diabaikan?" _))
;; binding diabaikan? 0
;; binding diabaikan? 1
;; binding diabaikan? 2
;; binding diabaikan? 3
;; binding diabaikan? 4
;; => nil
```

Fungsi `dotimes` tidak bisa melakukan *binding* dengan terhadap struktur data koleksi (misalnya kita ingin melakukan perulangan terhadap elemen di koleksi). Berikut adalah contoh saat kita mencoba *binding* ke koleksi dengan `dotimes`. Perhatikan di pesan errornya terdapat `... cannot be cast to class java.lang.Number` yang berarti *binding* harus dalam bentuk bilangan.

```clojure 
user> (dotimes [n "test"] ;; string adalah koleksi dari character
        (println n))
;; Execution error (ClassCastException) at user/eval7543 (REPL:80).
;; class java.lang.String cannot be cast to class java.lang.Number (java.lang.String and java.lang.Number are in module java.base of loader 'bootstrap')

user> (dotimes [n '(1 2 3)]
        (println n))
;; Execution error (ClassCastException) at user/eval7546 (REPL:84).
;; class clojure.lang.PersistentList cannot be cast to class java.lang.Number (clojure.lang.PersistentList is in unnamed module of loader 'app'; java.lang.Number is in module java.base of loader 'bootstrap')
```
Clojure sudah menyediakan fungsi untuk melakukan perulangan terhadap struktur data koleksi, yaitu `doseq`.


## doseq
Dengan fungsi `doseq`, kita bisa melakukan perulangan berdasarkan suatu koleksi. Semua elemen dari koleksi tersebut akan di-*bind* ke symbol yang kita bisa pakai di dalam perulangan. Sehingga, tidak ada akses elemen berdasarkan indeksnya. Jika `dotimes` tidak bisa *binding* dengan struktur data koleksi, maka `doseq` tidak bisa *binding* dengan nilai skalar seperti integer dan float (harus dalam bentuk koleksi). Selain itu, sama seperti `dotimes`, perulangan dengan `doseq` biasanya digunakan untuk mengevaluasi ekspresi dengan *side effects*. Jika di dalam `doseq` tidak ada ekspresi dengan *side effects*, kita hanya akan melihat `nil`.

```clojure 
user> (doseq [n '(1 2 3)]
        (str "nilai n adalah " n)) ;; perulangan fungsi murni
;; => nil

user> (doseq [n '(1 2 3)]  ;; bind tiap elemen '(1 2 3) ke n
        (println n))
;; 1
;; 2
;; 3
;; => nil

user> (doseq [n "test"] ;; string dianggap koleksi character
        (println n))
;; t
;; e
;; s
;; t
;; => nil
```
`doseq` juga bisa digunakan untuk melakukan perulangan terhadap struktur data map. Berbeda dengan list atau vector yang perulangannya dilakukan tiap elemen, perulangan di map didasarkan pada satu pasang entri di dalamnya. Tiap pasangan ini akan ditampilkan sebagai vector. Berikut adalah contoh perulangan terhadap map:

```clojure 
user> (def tes-map {:nama "Clojure"
                    :kreator "Rich Hickey"
                    :tahun-rilis 2007})
;; => #'user/tes-map
user> (doseq [entri tes-map]
        (println entri))
;; [:nama Clojure]
;; [:kreator Rich Hickey]
;; [:tahun-rilis 2007]
;; => nil
```
Karena entri map di perulangan diubah menjadi vector, kita tidak bisa mengaksesnya dengan keyword, melainkan dengan indeks.

```clojure 
user> (doseq [entri tes-map]
        (println (:nama entri)))
;; nil
;; nil
;; nil
;; => nil
user> (doseq [entri tes-map]
        (println (get entri 0))) ;; bisa dengan get
;; :nama
;; :kreator
;; :tahun-rilis
;; => nil
user> (doseq [entri tes-map]
        (println (entri 1)))    ;; atau tanpa get, karena vector juga bisa bertindak sebagai fungsi
;; Clojure
;; Rich Hickey
;; 2007
;; => nil
```

`doseq` juga mendukung *multibinding* dan bekerja seperti *nested loops*. Namun, berbeda dengan `dotimes`, kita tidak perlu menambahkan `doseq` bersarang. Tinggal dimasukkan saja di dalam vector. Berikut adalah contoh `doseq` dengan *binding* banyak:

```clojure 
user> (doseq [kursi [1 2 3]
              nama ["ani" "budi" "cindy"]]
        (println "kursi" kursi "dapat ditempati oleh" nama))
;; kursi 1 dapat ditempati oleh ani
;; kursi 1 dapat ditempati oleh budi
;; kursi 1 dapat ditempati oleh cindy
;; kursi 2 dapat ditempati oleh ani
;; kursi 2 dapat ditempati oleh budi
;; kursi 2 dapat ditempati oleh cindy
;; kursi 3 dapat ditempati oleh ani
;; kursi 3 dapat ditempati oleh budi
;; kursi 3 dapat ditempati oleh cindy
;; => nil
```

## while 
Clojure memiliki fungsi `while` yang bisa digunakan untuk melakukan perulangan terus-menerus selama memenuhi kondisi. Secara sintaksis, ia mirip dengan fungsi `when` yang sudah kita lihat di bagian 7. Namun, perbedaannya adalah ekspresi di dalam `when` dievaluasi satu kali, sedangkan ekspresi di dalam `while` dievaluasi berkali-kali.

Mari kita coba buat perulangan dengan mencetak bilangan dari 10 sampai 1 dengan `while`. Silakan pahami dulu contoh kode di bawah ini dan **jangan dimasukkan dalam REPL terlebih dahulu**. Kira-kira apa yang akan terjadi jika kita mengevaluasinya?

```clojure 
;; !!! CONTOH DI BAWAH INI JANGAN DIMASUKKAN DALAM REPL !!!
(def counter 10)

(while (pos? counter) ;; cek apakah counter masih positif
    (println "iterasi ke: " counter)
    (dec counter)) ;; nilai couter minus 1
```
Mari kita coba analisis bersama kode di atas. Kita harus kembali lagi ke prinsip dasar data di Clojure, yang mana mereka bersifat *immutable*. Karena bersifat *immutable*, ekspresi `(dec counter)` akan mengembalikan nilai `counter` yang sudah dikurangi 1, namun `counter` itu sendiri **tetap bernilai `10`**. Karena nilai `counter` tidak berubah, maka ekspresi `(pos? counter)` akan selalu bernilai `true`, yang akan membuat perulangan di atas berjalan selamanya. Jika nilai `counter` berhasil dikurangi, maka perulangan di atas akan berhenti saat `counter` benilai `0`. Karena pada saat itulah ekspresi `(pos? counter)` akan bernilai `false`.

Dari analisis di atas, bisa disimpulkan bahwa kode tersebut tidak bekerja sesuai dengan yang kita harapkan.

> Jika kode di atas terlanjur dimasukkan di dalam REPL Spacemacs, silakan *restart* Spacemacs dan mulai sesi REPL baru.

Untuk mengatasi masalah di atas, kita harus memakai ***mutable data***. Dengan *mutable data* kita bisa mengubah nilai dari suatu variabel/symbol (*side effect*), alih-alih hanya mengembalikan nilai yang sudah diubah. Akan tetapi, fungsi-fungsi yang sudah kita temui tidak bisa bekerja dengan *mutable data* ini karena mereka fungsi murni. Kita harus memakai fungsi khusus untuk melakukan operasi terhadap *mutable data* ini. 

Pembahasan tentang *mutable data* akan dilakukan di tulisan yang akan datang. Untuk saat ini, perhatikan contoh perulangan `while` dengan *mutable data* di bawah ini dan bandingkan dengan versi di atas. Untuk membuat *mutable data*, kita harus membungkus bilangan `10` dalam `atom`. Selain itu, untuk mengakses nilai dari *mutable data*, kita harus memakai tanda `@`. Terakhir, untuk mengurangi nilai `counter`, kita harus memakai fungsi `swap!`, yang akan menerapkan fungsi `dec` ke `counter`. Dengan `swap!`, nilai `counter` akan dikurangi dan perulangan akan berhenti saat `counter` bernilai `0`. Perlu diperhatikan bahwa dengan fungsi `swap!` kita tidak perlu memakai tanda `@` pada `counter`.

```clojure 
user> (def counter (atom 10)) ;; mutable data
;; => #'user/counter
user> (while (pos? @counter)
        (println "iterasi ke:" @counter)
        (swap! counter dec))  ;; fungsi khusus mutable data
;; iterasi ke: 10
;; iterasi ke: 9
;; iterasi ke: 8
;; iterasi ke: 7
;; iterasi ke: 6
;; iterasi ke: 5
;; iterasi ke: 4
;; iterasi ke: 3
;; iterasi ke: 2
;; iterasi ke: 1
;; => nil
```

Jangan khawatir jika perulangan `while` di atas terlalu kompleks dibandingkan dengan `while` di bahasa pemrograman lain. Kita akan jarang melihat contoh di atas. Fungsi `while` lebih sering dipakai untuk melalukan perulangan terus-menerus tanpa memedulikan *state* dari suatu variabel. Di Clojure ada banyak cara yang lebih mudah untuk melakukan perulangan di atas selain menggunakan `while`.

## for: untuk list-comprehension
Seperti yang sudah saya sebut di atas, Clojure juga memiliki fungsi `for`. Akan tetapi, ia bukan untuk perulangan tapi untuk ***list comprehension*** atau membangun sebuah list/sequence. Fungsi `for` bisa untuk perulangan biasa tapi perhatikan nilai kembaliannya di contoh di bawah ini:

```clojure 
user> (for [a (vector 1 2 3)] ;; bind elemen [1 2 3] dengan a
        (println a))
;; 1
;; 2
;; 3
;; => (nil nil nil)
```
Dari contoh di atas, bilangan `1`, `2`, dan `3` yang ditampilkan adalah *side effect* dari fungsi `println`. Di bagian bawah, kita mendapatkan list yang berisi 3 nilai `nil`. List tersebut adalah nilai kembalian dari fungsi `for`. Karena ekspresi `(println a)` mengembalikan `nil` dan diulang 3 kali, maka nilai kembalian dari ekspresi di atas adalah `(nil nil nil)`. Jika hanya ingin menampilkan elemen di dalam koleksi, maka lebih baik pakai fungsi `doseq`.

Jika masih bingung, mari buka dokumentasi dari fungsi `for`. Dari dokumentasi tersebut, kita bisa melihat di bagian awal bahwa fungsi `for` adalah untuk *list comprehension* dan menghasilkan *lazy sequence* dari proses evaluasi ekspresi di dalamnya. Untuk sekarang mohon abaikan kata *lazy* tersebut dan fokus pada *sequence* saja.

```clojure 
user> (doc for)
;; -------------------------
;; clojure.core/for
;; ([seq-exprs body-expr])
;; Macro
;;  List comprehension. Takes a vector of one or more
;;   binding-form/collection-expr pairs, each followed by zero or more
;;   modifiers, and yields a lazy sequence of evaluations of expr.
 ;;  Collections are iterated in a nested fashion, rightmost fastest,
;;   and nested coll-exprs can refer to bindings created in prior
;;   binding-forms.  Supported modifiers are: :let [binding-form expr ...],
;;   :while test, :when test.

;; => nil
```
Dari penjelasan di atas, kita bisa membuat sebuah struktur data koleksi yang lebih kompleks dari koleksi-koleksi yang ada. Atau dengan kata lain **collection in, collection out**. Mirip seperti `doseq`, kita harus *bind* elemen koleksi ke suatu symbol. Berikut adalah contoh pembuatan koleksi yang berisi hasil kuadrat dari koleksi input. Koleksi input kita buat dengan fungsi `range`.

```clojure 
user> (range 6)
;; => (0 1 2 3 4 5)
;; list isi kuadrat
user> (for [x (range 6)]
        (* x x))
;; => (0 1 4 9 16 25)
```

*Binding* dalam `for` harus dalam bentuk koleksi. Jika tidak, maka akan error. 

```clojure 
user> (for [x (range 6)
            x-kuadrat (* x x)] ;; bind dengan integer
        [x-kuadrat])
;; Error printing return value (IllegalArgumentException) at clojure.lang.RT/seqFrom (RT.java:557).
;; Don't know how to create ISeq from: java.lang.Long
```

Selain itu kita juga bisa memfilter elemen mana saja yang kita inginkan dengan `:when` atau `:while`. Di kedua filter tersebut, kita harus memberikan fungsi predikat yang akan dipakai untuk memeriksa tiap elemen apakah sesuai dengan kriteria. Berikut adalah perbedaan `:when` dan `:while` sebagai filter dalam fungsi `for`:

* `:when` akan memeriksa semua elemen, terlepas dari hasil pemeriksaanya bernilai `true` atau `false`.
* `:while` akan berhenti jika menemui `false`. 

Sebagai contoh, mari kita buat sebuah list yang berisi hasil kuadrat yang bernilai ganjil dari bilangan `11` sampai `15`. Kita akan memakai fungsi `odd?` untuk memeriksa apakah suatu nilai ganjil.

```clojure 
;; tanpa filter. sebagai pembanding
user> (for [n [11 12 13 14 15]]
        (* n n))
;; => (121 144 169 196 225)

;; dengan filter :when
user> (for [n [11 12 13 14 15]
            :when (odd? (* n n))]
        (* n n))
;; => (121 169 225)

;; dengan filter :while, yang akan berhenti saat bertemu false
user> (for [n [11 12 13 14 15]
            :while (odd? (* n n))]
        (* n n))
;; => (121)
```
Karena hasil kuadrat dari `12` bukan ganjil, maka filter `:while` akan berhenti dan `for` hanya akan mengembalikan `(121)` karena itulah satu-satunya nilai yang masuk kriteria filter `:while`. Sementara itu, filter `:when` akan mengembalikan semua elemen yang sesuai dengan fungsi predikatnya.

Berikut adalah contoh *list comprehension* yang lebih kompleks, di mana kita ingin membuat sebuah list yang berisi vector dengan 2 elemen (`x` dan `y`) mulai dari `0` sampai `4`. Kriteria yang diinginkan adalah `x` harus **lebih kecil** dari `y` dan perkalian antara `x` dan `y` harus **genap**.

```clojure 
user> (for [x (range 5)
            y (range 5)
            :when (< x y)
            :when (even? (* x y))]
        [x y])
;; => ([0 1] [0 2] [0 3] [0 4] [1 2] [1 4] [2 3] [2 4] [3 4])
```

Dengan fungsi `for`, kita juga dapat membuat sebuah list yang berisi map. Berikut adalah contoh pemetaan 3 nama orang dengan 3 kursi dalam map.

```clojure 
user> (for [kursi [:1 :2 :3]
            nama  ["ani" "budi" "cindy"]]
        {kursi nama})
;; => ({:1 "ani"} {:1 "budi"} {:1 "cindy"} 
;; =>  {:2 "ani"} {:2 "budi"} {:2 "cindy"}
;; =>  {:3 "ani"} {:3 "budi"} {:3 "cindy"})
```
Sampai di sini, saya harap kalian sudah paham bahwa fungsi `for` adalah bukan untuk perulangan, melainkan untuk *list comprehension*.

## loop & recur

Sampai di sini, perulangan yang sudah dipelajari di atas adalah bentuk iterasi biasa. Bentuk perulangan selanjutnya adalah rekursi dengan kombinasi `loop` dan `recur`. Fungsi `loop` bekerja seperti `let` yang bisa digunakan untuk *binding*. Mari kita coba bandingkan `let` dan `loop` untuk *binding*.

```clojure 
user> (let [x 5
            y 6]
        [x y])
;; => [5 6]

user> (loop [x 5
             y 6]
        [x y])
;; => [5 6]
```

Dari contoh di atas, kita bisa melihat `loop` bisa digunakan untuk *binding* seperti `let`. Namun, perbedaannya adalah `loop` bisa menjadi ***recursion point*** atau **titik rekursi**. Titik rekursi ini yang akan menjadi titik balik saat perulangan. Nah, untuk kembali ke titik rekursi, kita gunakan fungsi `recur`. Sehingga, `loop` dan `recur` jadi kombinasi untuk melakukan perulangan.

Di dalam fungsi `recur`, kita boleh memasukkan memasukkan ekspresi. Ekspresi ini akan di-*bind* ulang di dalam titik rekursi di `loop`. Selain itu, kita harus memeriksa nilai *binding* dalam tiap perulangan agar tidak mengulang selamanya. 

Sebagai contoh, mari kita coba menampilkan nilai dari 5 sampai 1 secara berulang dengan `loop` dan `recur`.

```clojure 
user> (loop [x 5]           ;; binding
        (when (pos? x)      ;; pengecekkan nilai
          (println x)  
          (recur (dec x)))) ;; kembali ke titik rekursi dengan nilai x - 1
;; 5
;; 4
;; 3
;; 2
;; 1
;; => nil
```

Dari contoh di atas, pertama kita harus *bind* nilai `5` ke symbol `x` di `loop` *binding*. Lalu kita periksa apakah `x` bernilai positif. Jika positif, tampilkan nilai `x`. Lalu, dengan fungsi `recur`, kita harus kembali ke titik rekursi di `loop`. Di `recur` inilah kita harus memberikan *binding* baru. Dalam hal ini, ekspresi `(dec x)` akan mengurangi nilai `x` dan hasilnya akan dipakai di dalam `loop` *binding*. Di perulangan terakhir saat `x` bernilai `0`, ekspresi `(pos? x)` akan bernilai `false`, yang mana akan menghentikan perulangan karena fungsi `when` hanya akan mengevaluasi saat `false`. Dengan proses seperti ini, perulangan akan "berjalan" sesuai yang kita harapkan.

Perlu dicatat bahwa jumlah ekspresi untuk *binding* baru di `recur` harus sesuai dengan jumlah *binding* di `loop`. Contoh di atas hanya ada 1 *binding* di `loop`, maka `recur` pun hanya perlu 1 ekspresi. Jika ada 2 *binding* di `loop`, maka `recur` perlu 2 ekspresi juga seperti `(recur (dec x) (dec y))`. Jika di `loop` tidak ada binding, maka `recur` juga harus tanpa argumen. Namun ini akan bekerja seperti `while true` yang akan mengulang selamanya.

Mari kita coba buat contoh `loop` dan `recur` lagi. Contoh di bawah mengembalikan vector baru yang mana tiap elemennya adalah hasil kuadrat dari vector asal (yang ada di *binding*). 

```clojure 
user> (loop [xs [1 2 3 4]  ;; vector asal.
             hasil []]     ;; vector kosong untuk menyimpan hasil kuadrat.
        (if xs             ;; cek apakah vector masih ada elemennya.
          (let [x (first xs)] ;; ambil elemen pertama vector.
            (recur (next xs) (conj hasil (* x x))))
          hasil)) ;; kembalikan hasilnya.
;; => [1 4 9 16]
```

Contoh di atas sudah saya beri komentar yang saya harap mudah dipahami dan di sini saya akan fokus ke bagian `recur`. Fungsi `recur` di atas menerima 2 ekspresi: `(next xs)` dan `(conj hasil (* x x))`. Fungsi `next` mirip seperti dengan `rest` yang akan mengembalikan koleksi tanpa nilai pertamanya. Perbedaannya adalah, saat bertemu koleksi kosong atau dengan 1 elemen, `rest` mengembalikan list/sequence kosong `()`, sedangkan next akan mengembalikan `nil`. `nil` ini digunakan dalam ekspresi `(if xs ...` sebagai tanda semua elemen sudah diproses dan perulangan harus berhenti.

```clojure 
user> (rest [4])
;; => ()
user> (next [4])
;; => nil
```
Ekspresi kedua - `(conj hasil (* x x))` - digunakan untuk menambahkan hasil kuadrat ke dalam vector `hasil` yang di *binding* awal masih kosong. Berikut adalah urutan kejadian yang ada di fungsi `recur` selama perulangan:

```clojure 
(recur [2 3 4] [1])    ;; perulangan ke-1
(recur [3 4] [1 4])    ;; perulangan ke-2
(recur [4] [1 4 9])    ;; perulangan ke-3
(recur nil [1 4 9 16]) ;; perulangan ke-4

;; karena xs bernilai nil di perulangan ke-5 
;; maka [1 4 9 16] akan dikembalikan.
;; stop.
```
Contoh selanjutnya adalah perhitungan nilai faktorial dengan rekursi menggunakan `loop` dan `recur`. Strukturnya mirip dengan contoh vector kuadrat di atas.

```clojure 
;; factorial
user> (loop [counter 5
             acc 1]         ;; akumulator untuk menyimpan hasil faktorial.
        (if (zero? counter) ;; cek apakah counter bernilai 0.
          acc               ;; jika 0, kembalikan akumulator
          (recur (dec counter) (* acc counter))))
;; => 120

```

Jika di contoh sebelumnya saya menunjukkan contoh urutan kejadian di fungsi `recur`, sekarang gantian dengan urutan kejadian di `loop` *binding*.

```clojure 
(loop [counter 5 acc 1])   ;; perulangan ke-1
(loop [counter 4 acc 5])   ;; perulangan ke-2
(loop [counter 3 acc 20])  ;; perulangan ke-3
(loop [counter 2 acc 60])  ;; perulangan ke-4
(loop [counter 1 acc 120]) ;; perulangan ke-5
(loop [counter 0 acc 120]) ;; perulangan ke-6. stop di sini.
```

## defn & recur
Selain fungsi `loop`, fungsi `defn` juga bisa digunakan untuk membuat titik rekursi. Ia digunakan untuk mendefinisikan sebuah fungsi. Jika kita kombinasikan `defn` dengan `recur`, maka kita dapat membuat fungsi rekursi yang akan memanggil dirinya sendiri. Namun, proses rekursi yang dilakukan tidak secara eksplisit karena `recur` dapat mengenali titik rekursi. Materi tentang fungsi akan dibahas di tulisan yang akan datang. Untuk saat ini, berikut adalah contoh vector kuadrat dan faktorial yang diimplementasikan menggunakan `defn` dan `recur`.

```clojure 
user> (defn rekursi [x]
        (when (pos? x)
          (println x)
          (recur (dec x))))
;; => #'user/rekursi
user> (rekursi 0)
;; => nil
user> (rekursi 1)
;; 1
;;=> nil
user> (rekursi 5)
;; 5
;; 4
;; 3
;; 2
;; 1
;; => nil 

user> (defn faktorial [xs hasil]
        (if xs
          (let [x (first xs)]
            (recur (next xs) (conj hasil (* x x))))
          hasil))
;; => #'user/faktorial
user> (faktorial [1 2 3 4] [])
;; => [1 4 9 16]
user> (faktorial [11 12] [])
;; => [121 144]
```

Jika diperhatikan, struktur kode antara menggunakan `loop` dan `defn` sangat mirip. Bedanya, karena sudah didefinisikan dalam fungsi, untuk melakukan perulangan kita cukup memanggil nama fungsi beserta argumennya tanpa perlu mengetik kode secara lengkap.

## Penutup

Di bagian ini kita sudah belajar cara melakukan perulangan di Clojure, baik itu dengan iterasi biasa atau rekursi. Kita melihat bahwa fungsi `for` di Clojure tidak sama seperti fungsi `for` di bahasa pemrograman lain. Selama menulis program Clojure nanti, kita tidak akan sering menggunakan perulangan secara eksplisit seperti yang kita lakukan di bagian ini. Ini karena Clojure adalah bahasa pemrograman fungsional dan sudah menyediakan banyak fungsi yang akan membantu kita melakukan perulangan secara implisit. Jika harus membuat perulangan sendiri, Clojure sangat menyarankan dalam bentuk rekursi tanpa *side effects*. 

Di bagian selanjutnya, kita akan belajar cara membuat fungsi di Clojure.
