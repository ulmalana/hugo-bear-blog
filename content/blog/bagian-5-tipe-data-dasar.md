+++
title = "Bagian 5: Tipe Data Dasar"
date = "2023-01-21T17:04:57+07:00"

#
# description is optional
#
description = "Bagian 5: Mengenal tipe data dasar Clojure seperti integer, string, boolean, dan lain-lain."

tags = ["clojure","tutorial",]
+++


Setelah mengenal struktur bahasa Clojure di [bagian 4](https://riz.maulana.me/blog/2023/01/bagian-4-struktur-dan-sintaksis/), kita akan belajar macam-macam tipe data yang ada di Clojure. Meskipun berjalan di JVM (yang mana didesain untuk Java yang *statically-typed*), Clojure adalah bahasa pemrograman yang *dynamically-typed* dan bisa menyimpulkan tipe data yang kita pakai secara otomatis. Sehingga kita tidak perlu menandai tipe data yang kita inginkan seperti `int x = 20;`. Sebenarnya kita bisa memaksakan tipe data yang kita inginkan seperti bahasa pemrograman *statically-typed* menggunakan [clojure.spec](https://clojure.org/about/spec), tapi itu topik lanjutan dan tidak akan dibahas di bagian ini. Di bagian ini kita akan mulai dari tipe data skalar (seperti bilangan, string, boolean, dsb), dan di bagian berikutnya kita akan masuk ke struktur data koleksi seperti vector, list, dsb. 
 
## Nil
Kita mulai dengan tipe data yang bisa dibilang paling sederhana, yaitu `nil`. Ia mirip dengan `null` di bahasa pemrograman lain dan merepresentasikan tidak adanya sebuah nilai. Nanti kita akan melihat kalau `nil` juga bisa dipakai sebagai penanda "ujung akhir" dari struktur data list.

Kita bisa memeriksa apakah sesuatu bernilai `nil` dengan fungsi `nil?`.

```clojure
user> (nil? nil)
;; => true
user> (nil? 42)
;; => false
```

> **Catatan**:
>
> Tanda tanya `?` di fungsi `nil?` adalah konvensi di Clojure yang menandakan bahwa ia adalah fungsi predikat. Fungsi predikat akan mengembalikan `true` atau `false`, tergantung dari hasil pemeriksaan. Dengan tanda tanya, fungsi `nil?` lebih terasa seperti sedang bertanya "apakah nilai ini bernilai `nil`?". Nanti kita akan melihat fungsi predikat yang lain.

> **Cek tipe data**
>
> Selain fungsi predikat, kita bisa memeriksa tipe data atau class dari suatu entitas dengan fungsi `type`. Contoh penggunaanya akan ditunjukan di bawah.

## Boolean
Tipe data boolean ini sama seperti di bahasa lain, yaitu untuk merepresentasikan benar dan salah, dan kata yang dipakai adalah `true` dan `false`. Di Clojure, `nil` *secara logika* diperlakukan sama seperti `false`. Jadi, selain`false` dan `nil`, **semua nilai** secara logika akan dianggap sebagai `true`. Clojure juga memiliki fungsi predikat untuk memeriksa apakah suatu entitas bernilai `true` atau `false` dengan fungsi `true?` dan `false?`. 

```clojure
user> (true? true)
;; => true
user> (true? false)
;; => false
user> (false? false)
;; => true
user> (false? nil) ;; 1
;; => false
user> (false? 42)  ;; 2
;; => false
user> (true? 42)  ;; 3
;; => false
user> (true? "string") ;; 4
;; => false
```
Fungsi predikat boolean ini hanya akan memeriksa nilai `true` dan `false`. Jadi, meskipun semua nilai selain `false` dan `nil` dianggap `true`, ia tetap mengembalikan `false` seperti di nomor 1-4. Ini dikarenakan nilai-nilai tersebut bukan `true` ataupun `false`.

Beberapa operasi dasar boolean sama seperti yang ada di bahasa pemrograman pada umumnya, antara lain:

1. `=`, `==`, `not=`, `<`, `<=`, `>`, `>=` untuk memeriksa kesamaan nilai. Dibandingkan dengan `=`, fungsi `==` lebih spesifik hanya untuk membandingkan `Number`.
2. `and` dan `or` untuk memeriksa nilai boolean tiap ekspresi.
3. `not` untuk menegasikan suatu nilai. 

```clojure
user> (= false false)
;; => true
user> (not= false true)
;; => true
user> (= "test" "test")
;; => true
user> (= "test" 1)
;; => false
user> (== "test" "test")
;; Execution error (ClassCastException) at user/eval7591 (REPL:197).
;; class java.lang.String cannot be cast to class java.lang.Number (java.lang.String and java.lang.Number are in module java.base of loader 'bootstrap')
user> (== 42 42)
;; => true
user> (not false)
;; => true
user> (not nil)
;; => true
user> (not 42)
;; => false
user> (not "test")
;; => false
```

Fungsi `and` dan `or` dapat *short-circuit*, yang berarti ia tidak akan mengevaluasi semua argumennya dan langsung mengembalikan nilai. Kedua fungsi itu mengevaluasi argumen dari kiri ke kanan dan berhenti jika memenuhi kriteria berikut:

1. `and` berhenti saat menemui `false` atau `nil`. Jika tidak menemui `false` atau `nil`, argumen paling akhir akan dikembalikan.

	```clojure
	user> (and 1 2 3)
	;; => 3
	user> (and 1 false 3)
	;; => false
	user> (and 1 nil 3)
	;; => nil
	```

2. `or` akan berhenti saat bertemu `true` atau nilai selain `false` dan `nil`. Jika tidak menemui satupun nilai yang bernilai `true`, argumen paling akhir akan dikembalikan.

	```clojure
	user> (or 1 2 3)
	;; => 1
	user> (or false nil 3)
	;; => 3
	user> (or false nil false)
	;; => false
	user> (or nil false nil)
	;; => nil
	```

## Bilangan
Clojure memiliki beberapa tipe data bilangan yang mungkin kamu sudah familiar atau belum.

### Integer
Integer digunakan untuk merepresentasikan bilangan bulat. Secara default, semua nilai integer akan disimpan sebagai Java Long yang bisa menyimpan bilangan bulat dari -9.223.372.036.854.775.808 sampai 9.223.372.036.854.775.807. Selain itu, integer juga memiliki fungsi predikat `int?`. Jika tanda `?` pada fungsi predikat `int?` dibuang, kita akan mendapatkan fungsi `int` yang dapat dipakai untuk mengkonversi tipe data bilangan lain ke integer.

```clojure 
user> (type 42)
;; => java.lang.Long
user> (int? 29)
;; => true
user> (int? 3.14)
;; => false
user> (int 22/7)
;; => 3
user> (int 9.9999)
;; => 9
user> (int "2")
;; Execution error (ClassCastException) at user/eval7710 (REPL:372).
;; class java.lang.String cannot be cast to 
;; class java.lang.Character (java.lang.String and
;;  java.lang.Character are in module java.base of loader 'bootstrap')
```

Integer mendukung operasi aritmatika dasar dan juga operasi matematis lainnya. Kita juga bisa memeriksa apakah dua integer bernilai sama dengan fungsi `=` atau `==`. Fungsi `>` dan `<` dapat dipakai untuk memeriksa apakah argumennya berurutan dari besar ke kecil atau sebaliknya.

```clojure
user> (+ 3 2)
;; => 5
user> (* 5 4)
;; => 20
user> (- 6 9)
;; => -3
user> (/ 100 4)
;; => 25
user> (= 32 32)
;; => true
user> (== 32 21)
;; => false
user> (> 42 30 18 17 10) ;; berurutan dari besar ke kecil
;; => true
user> (> 10 9 9 8)
;; => false
user> (< 3 5 7 8 10) ;; berurutan dari kecil ke besar
;; => true
user> (< 3 4 2 5)
;; => false
```

Ada beberapa fungsi terkait operasi pembagian, antara lain:
* `quot` untuk mendapatkan hasil pembagian.
* `rem` untuk mendapatkan sisa pembagian.
* `mod` mirip seperi `rem`, namun akan memberikan hasil yang berbeda jika berhubungan dengan nilai negatif.

Dokumentasi Clojure sudah memberikan penjelasan perbedaan antara `rem` dan `mod` di halaman [ini](https://clojuredocs.org/clojure.core/rem).

```clojure 
user> (quot 22 7)
;; => 3
user> (rem 22 7)
;; => 1
user> (mod 22 7)
;; => 1
user> (mod -10 3)
;; => 2
user> (rem -10 3)
;; => -1
```

Apabila Long tidak cukup untuk menyimpan bilangan yang diinginkan, kita bisa menaruh `N` di belakang bilangan agar menjadi `BigInt`. Sebagai contoh, mari kita coba tambahkan bilangan paling besar di Java Long (9.223.372.036.854.775.807) dengan 10, yang akan mengakibatkan `long overflow`. Dengan menaruh `N` di belakang 10 *atau* 9.223.372.036.854.775.807, kita bisa mencegah *overflow*. Operasi yang melibatkan `BigInt` akan mengembalikan `BigInt` juga, sehingga kita hanya perlu menambahkan `N` di salah satu bilangan saja.

```clojure 
user> (+ 9223372036854775807 10)
;; Execution error (ArithmeticException) at java.lang.Math/addExact (Math.java:848).
;; long overflow
user> (+ 9223372036854775807 10N)
;; => 9223372036854775817N
user> (+ 9223372036854775807N 10)
;; => 9223372036854775817N
user> (type (+ 9223372036854775807 10N))
;; => clojure.lang.BigInt
user> (= 100 100N)
;; => true
user> (type 100N)
;; => clojure.lang.BigInt
```

### Ratio
Ratio digunakan untuk merepresentasikan pecahan. Saat integer tidak bisa dibagi habis, ia akan direpresentasikan sebagai pecahan. Pecahannya juga akan disederhanakan sampai ke bentuk yang tidak mungkin disederhanakan lagi.

```clojure 
user> 1/3
;; => 1/3
user> 7/21
;; => 1/3
user> 5/15
;; => 1/3
user> 12/10
;; => 6/5
user> (/ 1 3)
;; => 1/3
user> (/ 7 21)
;; => 1/3
user> (/ 22 7)
;; => 22/7
user> (type (/ 22 7))
;; => clojure.lang.Ratio
```

Untuk mengambil nilai pembilang atau penyebut dari suatu pecahan, kita bisa memakai fungsi `numerator` untuk mendapatkan pembilang dan `denominator` untuk mendapatkan penyebut.

```clojure 
user> (numerator 22/7)
;; => 22
user> (denominator 22/7)
;; => 7
user> (numerator (/ 7 21))
;; => 1
user> (denominator (/ 7 21))
;; => 3
```
Fungsi predikat untuk ratio adalah `ratio?`. Jika nilai ratio bisa disederhanakan menjadi integer seperti `4/2`, maka `ratio?` akan mengembalikan `false` karena `4/2` dianggap sebagai integer `2`. Selain itu, kita dapat mengkonversi bilangan lain ke bentuk pecahan dengan fungsi `rationalize`.
```clojure 
user> (ratio? 22/7)
;; => true
user> (ratio? 3.14)
;; => false
user> (ratio? 2)
;; => false
user> (ratio? 4/2)
;; => false
user> (rationalize 7)
;; => 7
user> (rationalize 1/4)
;; => 1/4
user> (rationalize 0.25)
;; => 1/4
user> (rationalize 3.45331)
;; => 345331/100000
```
### Bilangan dengan basis arbitrer
Clojure mendukung penulisan bilangan dengan basis selain 10 (desimal), seperti biner, oktal, dan heksadesimal. Sintaksis yang digunakan adalah `BrN`, di mana `B` adalah basis atau radiks, dan `N` adalah bilangan yang kita inginkan. Berikut adalah contoh representasi integer 42 dalam biner, oktal, dan heksadesimal:

```clojure 
user> 2r101010
;; => 42
user> 8r52 ;; atau 052
;; => 42
user> 16r2a ;; atau 0x2a
;; => 42
```
Basis yang bisa dipakai adalah sampai 36, karena itulah jumlah kombinasi alfabet (26) dan digit (10) yang dapat dipakai untuk membuat bilangan.
```clojure 
user> 20rHEHE
;; => 141954
user> 36rHEHE
;; => 811922
user> 36rZZZ
;; => 46655
user> 40rZZZ
;; Syntax error reading source at (REPL:147:1).
;; Radix out of range
```


### Floating Point
Floating point digunakan untuk merepresentasikan bilangan berkoma. Secara default ia disimpan sebagai Java Double. Kalau kita menginginkan bentuk floating point (bukan pecahan) dari suatu pembagian, cukup ditambahkan titik `.` di salah satu nilai.

```clojure
user> 3.14
;; => 3.14
user> (type 3.14)
;; => java.lang.Double
user> (/ 22 7)
;; => 22/7
user> (/ 22. 7)
;; => 3.142857142857143
user> (/ 22.0 7)
;; => 3.142857142857143
```

Jika ingin meningkatkan presisi, kita bisa menempatkan `M` di belakang bilangan floating point agar ia menjadi `BigDecimal`. Namun, perlu diingat bahwa operasi yang melibatkan Double atau Float akan mengembalikan Double atau Float juga, yang mungkin akan memotong presisi. Jika hasil yang diinginkan harus dalam `BigDecimal` dengan presisi tinggi, kita harus menaruh `M` di semua bilangan.

```clojure 
user> 3.14M
;; => 3.14M
user> (type 3.14M)
;; => java.math.BigDecimal
user> (type 3.12345678901234567890)
;; => java.lang.Double
user> (type 3.12345678901234567890M)
;; => java.math.BigDecimal
user> (+ 1.0 3.12345678901234567890M) ;; akan terpotong
;; => 4.123456789012346
user> (+ 1.0M 3.12345678901234567890) ;; akan terpotong
;; => 4.123456789012346
user> (+ 1.0M 3.12345678901234567890M)
;; => 4.12345678901234567890M
```

Fungsi predikat untuk float adalah `float?` atau `double?`, dan fungsi konversinya adalah `float` atau `double`.

```clojure 
user> (float? 3.12345671234)
;; => true
user> (double? 3.12345671234)
;; => true
user> (float? 22/7)
;; => false
user> (double? 22/7)
;; => false
user> (float 22/7)
;; => 3.142857
user> (float 5)
;; => 5.0
user> (double 18/5)
;; => 3.6
user> (double 3)
;; => 3.0
user> (== 1.0 1M 1/1 1 1N) ;; membandingkan dalam banyak tipe
;; => true
user> (= 1.0 1M 1/1 1 1N) ;; fungsi = lebih strict
;; => false
```

## Character
Character dipakai untuk merepresentasikan sebuah karakter. Ia punya sintaksis yang unik karena diawali dengan garis miring `\`, bukan diapit oleh tanda petik `"` atau petik satu `'`. Jadi kalau kita ingin karakter `a`, ia harus ditulis sebagai `\a`.
```clojure 
user> \a
;; => \a
user> \9
;; => \9
user> (char? \c)
;; => true
user> (char? "c")
;; => false
```

Kita bisa memakai petik satu `'` sebelum sebuah karakter, tapi ia akan menjadi symbol, bukan character. Symbol akan dijelaskan setelah ini.

```clojure 
user> 'c
;; => c
user> (type 'c)
;; => clojure.lang.Symbol
user> (type \c)
;; => java.lang.Character
```
Clojure juga memiliki beberapa *special character* yang sulit untuk direpresentasikan per karakter, yaitu `\space` untuk spasi, `\tab` untuk tab, `\newline` untuk membuat baris baru. Selain itu, dengan fungsi `char` kita bisa mengkonversi nilai ASCII ke character.

```clojure
user> (char 53)
;; => \5
user> (char 65)
;; => \A
user> (char 97)
;; => \a
```

## String
String dipakai untuk merepresentasikan rangkaian dari character. Data literal string harus diapit oleh tanda petik dua `"`. Tanda petik satu `'` tidak bisa dipakai untuk string karena ia punya fungsi lain di Clojure.

```clojure
user> "halo"
;; => "halo"
user> (type "ini string")
;; => java.lang.String
user> (string? "benar")
;; => true
user> (string? \s)
;; => false
```

Karena string adalah rangkaian character, kita juga bisa membuat sebuah string dari beberapa character dengan fungsi `str`.

```clojure 
user> (str \h \a \l \o)
;; => "halo"
user> (str (char 82) (char 77))
;; => "RM"
user> (str \2 \space \+ \space \2 \space \= \space \4)
;; => "2 + 2 = 4"
user> (string? (str \2 \space \+ \space \2 \space \= \space \4))
;; => true
user> (str \H \newline \E \newline \H \newline \E \newline)
;; => "H\nE\nH\nE\n"
user> (print (str \H \newline \E \newline \H \newline \E \newline))
;; => H
;; => E
;; => H
;; => E
;; => nil
```

## Symbol
Symbol adalah *identifier* (atau nama) yang digunakan untuk merujuk ke entitas lain. Entitas ini biasanya adalah **var**, yang merupakan entitas yang dapat menyimpan nilai. Symbol biasanya dipakai untuk menamai sesuatu seperti fungsi dan variabel. Sebagai contoh, pada ekspresi `(+ 2 3)`, `+` adalah symbol. Jika ekspresi tersebut dievaluasi, `+` akan merujuk ke var yang berisi obyek fungsi untuk penjumlahan. Fungsi yang dirujuk symbol `+` inilah yang menerima `2` dan `3` untuk dijumlahkan. Contoh lainnya adalah fungsi `str` yang sudah kita lihat. Ia adalah symbol yang jika dievaluasi akan merujuk ke var yang berisi fungsi untuk menggabungkan string atau character. 

```clojure 
user> +
;; => #function[clojure.core/+]
user> (type +)
;; => clojure.core$_PLUS_
user> str
;; => #function[clojure.core/str]
user> (type str)
;; => clojure.core$str
```

Kalau kita ingin mendapatkan nama dari symbol itu sendiri (artinya tanpa dievaluasi), kita bisa pakai tanda petik satu `'` di depan symbol untuk memaksa Clojure tidak mengevaluasinya. Tanpa tanda petik satu `'`, Clojure akan mencoba mengevaluasi symbol tersebut. Saat proses evaluasi, jika ada var yang ditunjuk oleh symbol, maka kita akan mendapatkan nilai di dalam var. Jika tidak ada var yang ditunjuk, maka akan terjadi error. 

```clojure 
user> '+
;; => +
user> (type '+)
;; => clojure.lang.Symbol
user> (symbol? '+)
;; => true
user> 'str
;; => str
user> (type 'str)
;; => clojure.lang.Symbol
user> (symbol? 'str)
;; => true
user> (symbol? symbol-tanpa-var)
;; => Syntax error compiling at (*cider-repl ~:localhost:35247(clj)*:293:7).
;; => Unable to resolve symbol: symbol-tanpa-var in this context
user> (symbol? 'symbol-tanpa-var)
;; => true
user> (= 'symbol-1 'symbol-1)
;; => true
user> (= '+ '+)
;; => true
```
Kita bisa mengkonversi beberapa tipe data lain ke symbol dengan fungsi `symbol`.

```clojure 
user> (symbol "hasil-konversi")
;; => hasil-konversi
user> (symbol :ini-apa-ya?)
;; => ini-apa-ya?
user> (type (symbol :ini-apa-ya?))
;; => clojure.lang.Symbol
```

Jadi, di Clojure ada pemisahan antara nama dan sesuatu yang dirujuk oleh nama tersebut, seperti perbedaan antara nama orang dengan orang yang dinamai.

## Keywords
Jika symbol adalah nama yang merujuk ke entitas yang ia namai, maka keyword adalah nama itu sendiri. Ia lebih simpel karena ia merujuk ke dirinya sendiri (bukan ke var). Dengan begini, tanda petik satu `'` sudah tidak dibutuhkan lagi di keyword. Di Clojure, keyword selalu diawali dengan tanda titik dua `:`.

```clojure
user> :contoh
;; => :contoh
user> :45
;; => :45
user> (keyword? :betul)
;; => true
user> (type :tipenya-apa?)
;; => clojure.lang.Keyword
user> (symbol? :clojure)
;; => false
user> (= :key1 :key1)
;; => true
user> (= :key1 :key2)
;; => false
```
Bisa ditebak fungsi apa yang dipakai untuk mengkonversi ke keyword, kan? Yap, `keyword`.

```clojure
user> (keyword "konversi-string")
;; => :konversi-string
user> (keyword 'dari-symbol)
;; => :dari-symbol
user> (keyword 123)
;; => nil
user> (keyword '123)
;; => nil
user> (keyword "123")
;; => :123
```

Karena keyword ini sederhana dan efisien, ia sering dipakai sebagai kata kunci di struktur data maps, yang akan dibahas di bagian selanjutnya.

Bagi yang belum terbiasa dengan Lisp, konsep symbol dan keyword ini memang hal yang baru dan butuh waktu untuk memahami. Tapi tidak usah khawatir karena ini hanya soal seberapa sering kita terpapar dengan konsep baru. Makin sering terpapar maka seharusnya kita akan makin terbiasa.


## Menyimpan Nilai
Sampai di sini, kita sudah mengenal beberapa tipe data dasar dan juga melakukan operasi-operasi dasar terhadap mereka. Jika diperhatikan, semua nilai yang kita ketik di contoh-contoh di atas adalah data literal yang tidak disimpan di dalam memori. Setelah kita mengevaluasi sebuah ekspresi, ia akan segera ditampilkan tanpa disimpan. Lalu bagaimana cara menyimpan (atau di Clojure dikenal dengan istilah **bind** atau mengikat/mengasosiasikan) data literal ke suatu identifier/nama/variabel? Kita bisa memakai fungsi `def` dengan sintaksis sebagai berikut:

```clojure 
(def <nama> <ekspresi>)
```
`<nama>` adalah nama dari identifier/variabel itu sendiri, dan `<ekspresi>` adalah nilai atau ekspresi valid lainnya. Perlu diingat lagi bahwa data literal juga termasuk ekspresi yang mengembalikan dirinya sendiri. Sebagai contoh, mari kita coba *ikat* integer `42` dengan symbol `x`.
```clojure 
user> (def x 42)
;; => #'user/x
```

Sebentar... nilai kembalian `#'user/x` ini apa? Ia adalah var, entitas yang dapat merujuk ke suatu lokasi memori. Atau simpelnya, entitas yang dapat menyimpan nilai. Nama var ini terdiri dari dua bagian, yaitu namespace (`user`) dan symbol yang dipakai untuk menyimpan nilai (`x`). Tanda `#'`  di depan adalah untuk menandai bahwa ia adalah var.

```clojure 
user> (type #'user/x)
;; => clojure.lang.Var
```

Kita bisa mendapatkan nilai `42` melalui var (bukan symbol `x`) dengan fungsi `deref` atau makro `@`.

```clojure 
user> (deref #'user/x)
;; => 42
user> @#'user/x
;; => 42
```

Jadi, yang terjadi pada saat `x` dievaluasi adalah sebagai berikut:
1. Symbol `x` merujuk ke var `#'user/x`.
2. Var `#'user/x` merujuk ke lokasi memori tempat nilai `42` disimpan.
3. Nilai `42` akan dikembalikan.

```clojure
user> x
;; => 42
```

Dengan `def`, kita juga bisa membuat sebuah var kosong, yang tidak terikat dengan nilai manapun. Cukup masukan `<nama>` sebagai argumen tanpa diikuti `<ekspresi>`. Ia mirip seperti kotak kosong yang bisa dipakai untuk menyimpan nilai sementara. Kita bisa menggunakan fungsi `let` untuk mengisi var kosong ini dengan sebuah nilai di dalam *local scope*.

```clojure
user> (def kosong)
;; => #'user/kosong
user> kosong
;; => #object[clojure.lang.Var$Unbound 0x42b00be0 "Unbound: #'user/kosong"]
user> (deref #'user/kosong)
;; => #object[clojure.lang.Var$Unbound 0x42b00be0 "Unbound: #'user/kosong"]
user> (let [kosong "isi sementara"] 
        kosong) ;; Spacemacs: tekan CTRL+Enter untuk pindah baris
;; => "isi sementara"
user> kosong
;; => #object[clojure.lang.Var$Unbound 0x42b00be0 "Unbound: #'user/kosong"]
```
Ada yang perlu diingat lagi tentang data di Clojure, yaitu mereka **immutable**. Artinya, kita tidak bisa mengubah secara sembarangan. Alih-alih mengubah, yang kita dapatkan nantinya adalah nilai baru. Nilai yang lama akan tetap utuh. Dengan begini kita tidak perlu lagi membuat ekspresi seperti `x = x + 1` untuk memperbarui nilai x. Clojure punya fungsi `inc` yang akan mengembalikan nilai yang sudah di-inkremen. Nilai kembalian ini langsung bisa digunakan untuk komputasi lanjutan, daripada harus di-assign ulang. Nilai yang ditambahkan (`x`) tidak akan berubah. Kita bisa mengubah `x` ke nilai lain dalam *local scope* dengan `let`, tapi di luar *scope* ia akan kembali ke nilai lama.

```clojure 
user> (inc x)
;; => 43
user> x
;; => 42
user> (str "hasil inkremen x adalah: " (inc x))
;; => "hasil inkremen x adalah: 43"
user> (let [x (+ x 1)]
        x)
;; => 43
user> x
;; => 42
```

Jika benar-benar diinginkan, kita bisa memaksa Clojure untuk mengganti nilai var dengan memanggil `def` lagi. Lagipula, var juga merupakan salah satu cara untuk memakai *mutable data* karena *binding*-nya dapat diubah-ubah (atau lebih tepatnya didefinisikan ulang). Namun, sejauh pengalaman saya, var lebih jarang dipakai untuk menyimpan *mutable data* dibandingkan tipe referensi lain seperti Atom, Agent, dan Ref. Tapi itu untuk pembahasan lain waktu. 

```clojure
user> (def x 63)    ;; rebind ke 63
;; => #'user/x
user> x
;; => 63
user> (def x (+ 24 (* 2 3)))  ;; rebind
;; => #'user/x
user> x
;; => 30
user> (def y 5)
;; => #'user/y
user> (def z (* x y))
;; => #'user/z
user> z
;; => 150
user> (+ x y z)
;; => 185
```

## Penutup
Di bagian ini kita sudah belajar tipe data skalar di Clojure. Tipe dasar ini menjadi basis dari struktur data koleksi yang berisi sekumpulan skalar. Saya tidak membahas pemakaian memori tiap tipe data secara detail karena saya ingin mengajak berpikir di *high-level*. Biarlah JVM yang mengurus bagian *low-level* seperti manajemen memori. Kita juga sudah belajar cara menyimpan atau mengikat sebuah nilai dengan `def` dan melihat bagaimana *immutability* bekerja. Di bagian selanjutnya, kita akan melihat bagaimana Clojure menerapkan *immutability* dan *persistence* di struktur data koleksi.
