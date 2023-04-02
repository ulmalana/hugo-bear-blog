+++
title = "Bagian 9-b: Multi-arity dan Destrukturisasi Parameter"
date = "2023-04-02T17:02:59+07:00"

#
# description is optional
#
# description = "An optional description for SEO. If not provided, an automatically created summary will be used."

tags = ["clojure","tutorial",]
+++

Di bagian 9-a, kita sudah belajar cara membuat dan menggunakan fungsi di Clojure. Di bagian ini, kita akan mengulas fungsi di Clojure lebih dalam. Kita akan mengenal cara membuat fungsi yang dapat menerima jumlah argumen yang berbeda dan juga cara mengakses elemen dalam suatu argumen dalam bentuk koleksi. 

## Fungsi dengan *multi-arity*

Hal pertama yang akan kita pelajari adalah fungsi dengan *multi-arity*. Apa itu *arity*? ***Arity*** adalah jumlah argumen yang bisa diberikan kepada suatu fungsi. Sebagai contoh, fungsi `inc` yang bisa digunakan untuk *increment* memiliki *arity* 1, karena ia hanya menerima 1 argumen. Jika argumen yang diberikan kepada `inc` bukan 1, maka kita akan mendapatkan `ArityException`.

```clojure 
user> (inc 10)
;; => 11

;; mencoba arity 2
user> (inc 10 11)
;; Syntax error (ArityException) compiling inc at (*cider-repl ~:localhost:33537(clj)*:53:7).
;; Wrong number of args (2) passed to: clojure.core/inc--inliner--5601

;; arity 0
user> (inc)
;; Syntax error (ArityException) compiling inc at (*cider-repl ~:localhost:33537(clj)*:56:7).
;; Wrong number of args (0) passed to: clojure.core/inc--inliner--5601
```

Lalu, apa yang dimaksud dengan *multi-arity*? ***Multi-arity*** berarti sebuah fungsi dapat menerima ***jumlah argumen yang berbeda***. Fungsi `inc` di atas bukanlah fungsi dengan *multi-arity* karena hanya menerima 1 argumen. Jika `inc` memiliki *multi-arity*, maka ia seharusnya bisa menerima 0 atau lebih dari 1 argumen dan contoh REPL di atas pasti tidak memunculkan error.

*Multi-arity* ini biasanya dipakai untuk mendefinisikan argumen default dari suatu fungsi. Misalkan kita ingin membuat sebuah fungsi yang menampilkan pesan selamat datang kepada pengunjung. Jika pengunjung tersebut sudah terdaftar, kita tampilkan namanya. Jika belum terdaftar, anggaplah ia sebagai "Pengunjung yang budiman". Berikut adalah definisi fungsi dengan *multi-arity*.

```clojure 
user> (defn sambut
        ;; definisi untuk arity 0
        ;; "Pengunjung yang budiman" dianggap sebagai nilai default
        ([] (sambut "Pengunjung yang budiman"))
        ;; definisi untuk arity 1
        ([nama] (println (str "Selamat datang, " nama "."))))
;; => #'user/sambut

user> (sambut)
;; Selamat datang, Pengunjung yang budiman.
;; => nil

user> (sambut "Budi")
;; Selamat datang, Budi.
;; => nil

;; akan error karena arity 2 belum didefinisikan
user> (sambut "Ani" "Budi")
;; Execution error (ArityException) at user/eval9205 (REPL:102).
;; Wrong number of args (2) passed to: user/sambut
```

Dari contoh REPL di atas, kita bisa melihat bahwa badan fungsi untuk tiap *arity* didefinisikan satu kali. Jadi kita tidak perlu mengetik `defn` beberapa kali untuk tiap *arity*. Saat fungsi `sambut` tidak diberi argumen, maka ia akan memanggil dirinya sendiri namun dengan tambahan `Pengunjung yang budiman` sebagai nilai default. Dengan begitu, jika seorang pengunjung belum terdaftar, pesan sambutan tersebut masih tetap ditampilkan. Fungsi `sambut` di atas meskipun sudah *multi-arity*, tapi hanya bisa menangani 0 dan 1 argumen. Jika kita beri 2 argumen, ia akan error. Ini berarti kita harus mendefinisikan ulang dan menambahkan *arity* 2 kepada `sambut`. 

```clojure 
user> (defn sambut
        ([] (sambut "Pengunjung yang budiman"))
        ([nama] (println (str "Selamat datang, " nama ".")))
        ([nama1 nama2] (println (str "Selamat datang, " nama1 " & " nama2 "."))))
;; => #'user/sambut

user> (sambut "Ani" "Budi")
;; Selamat datang, Ani & Budi.
;; => nil
```

Berikut adalah contoh selanjutnya untuk fungsi menghitung nilai kubik dengan *multi-arity*. Dengan *multi-arity*, kita bisa meng-*handle* beberapa *user error* (seperti lupa jumlah argumen atau salah ketik) saat mereka memakai fungsi `kubik`.

```clojure 
user> (defn kubik
        ([] (println "Tidak menerima 0 argumen"))
        ([x] (kubik x x x))
        ([x y] (when (= x y)
                 (kubik x x x)))
        ([x y z] (when (= x y z)
                   (* x y z))))
;; => #'user/kubik

;; arity 0
user> (kubik)
;; Tidak menerima 0 argumen
;; => nil

;; arity 1
user> (kubik 3)
;; => 27

;; arity 2 - valid
user> (kubik 3 3)
;; => 27

;; arity 2 - invalid
user> (kubik 3 2)
;; => nil

;; arity 3 - valid
user> (kubik 3 3 3)
;; => 27

;; arity 3 - invalid
user> (kubik 3 3 4)
;; => nil
```

Sebelum membuat fungsi dengan *multi-arity*, kita harus perhatikan skenario seperti apa saja yang perlu ditangani dari fungsi yang kita buat agar dapat bekerja sesuai yang diharapkan.


## Destrukturisasi Parameter

Contoh fungsi yang sudah kita buat sejauh ini memiliki parameter nilai skalar seperti string, integer, boolean, dsb. Bagaimana jika kita ingin memberikan struktur data koleksi sebagai argumen kepada suatu fungsi? Bagaimana cara mengakses elemen dari koleksi tersebut? Kita bisa melakukan *destrukturisasi* atau pembongkaran parameter agar kita bisa mengakses nilainya secara lebih ringkas. 

Selain pada parameter fungsi, proses *destrukturisasi* ini bisa dilakukan dengan fungsi `let`. Mari kita coba lakukan *destrukturisasi* dengan `let` terlebih dahulu. Setelah itu, kita terapkan destrukturisasi di `let` kepada parameter fungsi.

Misalkan kita punya struktur koleksi untuk menyimpan biodata dua orang mahasiswa beserta nilai hasil ujiannya sebagai berikut (keduanya disimpan struktur yang berbeda sebagai contoh).

```clojure 
;; biodata dalam vector
user> (def bio-budi ["Budi" 20 :laki-laki])
;; => #'user/bio-budi

;; biodata + nilai dalam vector bersarang
user> (def nilai-budi ["Budi" 20 :laki-laki [70 85 80 91]])
;; => #'user/nilai-budi

;; biodata dalam maps
user> (def bio-dina {:nama "Dina" :umur 21 
                     :jenis-kelamin :perempuan})
;; => #'user/bio-dina

;; biodata + nilai dalam maps bersarang
user> (def nilai-dina {:nama "Dina" :umur 21 
                       :jenis-kelamin :perempuan 
                       :hasil-ujian {:ujian-1 78
                                     :ujian-2 66
                                     :ujian-3 97
                                     :ujian-4 93}})
;; => #'user/nilai-dina
```
### Destrukturisasi Vector
Baik di parameter fungsi ataupun di fungsi `let`, proses destrukturisasi terjadi di dalam sebuah vector. Seperti yang sudah dipelajari sebelumnya, tiap *binding* di dalam vector tersebut terdiri dari sepasang symbol dan nilai. Jika nilai yang akan kita proses disimpan dalam bentuk vector, maka symbol juga harus di dalam vector. Perhatikan contoh berikut.

```clojure 
;; tanpa destrukturisasi.
user> (let [data-utuh bio-budi]
        (println "Data utuh Budi" data-utuh))
;; Data utuh Budi [Budi 20 :laki-laki]
;; => nil

;; dengan  destrukturisasi.
user> (let [[nama usia jenis-kelamin] bio-budi]
        (println "Nama: " nama)
        (println "Usia: " usia)
        (println "Jenis kelamin: " jenis-kelamin))
;; Nama:  Budi
;; Usia:  20
;; Jenis kelamin:  :laki-laki
;; => nil
```
Di contoh tanpa destrukturisasi, kita menganggap `bio-budi` sebagai satu kesatuan dan dianggap sebagai skalar, sehingga kita *bind* dengan `data-utuh` secara langsung. Elemen dari `data-utuh` nantinya bisa diakses dengan fungsi `get`. Di contoh dengan destrukturisasi, kita ingin mengakses elemen dari `bio-budi`. Oleh karena itu, `data-utuh` diganti dengan sebuah vector yang mana elemennya adalah symbol yang merepresentasikan posisi tiap elemen. Symbol-symbol tersebut bersifat lokal dan arbitrer (artinya kita bisa menamai mereka apapun). Dari contoh di atas, kita *bind* `bio-budi` dengan vector `[nama usia jenis-kelamin]`. Dengan proses destrukturisasi seperti ini, kita bisa mengakses elemen dari `bio-budi` dengan nama yang sudah di-*bind*, yaitu `nama`, `usia`, dan `jenis-kelamin`. 

Perlu diperhatikan jika ada symbol yang tidak memiliki pasangan nilai, maka secara default symbol tersebut akan di-*bind* ke `nil`. Sebaliknya, bila jumlah symbol lebih sedikit dari jumlah elemen vector, maka elemen yang tidak di-*bind* akan diabaikan. Selain itu, proses destrukturisasi sebuah list sama seperti dengan vector. 

```clojure 
;; sekolah adalah symbol yang tidak memiliki pasangan nilai
;; sehingga dianggap sebagai extra binding dan
;; secara default bernilai nil
user> (let [[nama usia jkelamin sekolah] bio-budi]
        (println nama usia jkelamin sekolah))
;; Budi 20 :laki-laki nil
;; => nil

;; elemen ketiga dari bio-budi diabaikan
user> (let [[nama usia] bio-budi]
        (println nama usia))
;; Budi 20
;; => nil

;; proses destrukturisasi list sama seperti vector
user> (let [[a b c] '(1 2 3)]
        (println a b c))
;; 1 2 3
;; => nil
```

Jika kita ingin mengakses elemen di vector bersarang seperti `nilai-budi` di atas, tinggal disesuaikan saja struktur vectornya. Selain itu, jika ingin mengabaikan suatu elemen dalam vector, nama dari elemen tersebut bisa diganti dengan karakter `_`. Sebagai contoh, kita ingin mengakses elemen nama, usia, nilai ujian 2, dan ujian 3 saja dari `nilai-budi`. Proses destrukturisasinya adalah sebagai berikut.

```clojure 
;; tanpa destrukturisasi
user> (let [data-utuh nilai-budi]
        (println "Data utuh Budi" data-utuh))
;; Data utuh Budi [Budi 20 :laki-laki [70 85 80 91]]
;; => nil
 
;; dengan destrukturisasi
;; _ berarti elemen diabaikan
user> (let [[nama usia _ [_ u2 u3 _]] nilai-budi]
        (println nama "yang berusia" usia "tahun mendapatkan nilai" u2 "dan" u3))
;; Budi yang berusia 20 tahun mendapatkan nilai 85 dan 80
;; => nil
```

### Destrukturisasi Maps
Untuk mendestruktur maps, prosesnya mirip seperti dengan destrukturisasi vector di atas namun kita harus spesifikasikan keyword-nya. Keyword tersebut harus ada di dalam maps yang ingin kita proses. Selain itu, di dalam vector, kita memakai kurung kurawal `{}`, bukan kurung siku `[]`.

```clojure 
;; tanpa destrukturisasi
user> (let [data-utuh bio-dina]
        (println "Data utuh Dina" data-utuh))
;; Data utuh Dina {:nama Dina, :umur 21, :jenis-kelamin :perempuan}
;; nil

;; nilai yang terasosiasi dengan tiap keyword di bio-dina 
;; (:nama, :umur, :jenis-kelamin) akan di-bind 
;; ke nama, usia, dan kel
user> (let [{nama :nama
             usia :umur
             kel :jenis-kelamin} bio-dina]
        (println "Nama: " nama)
        (println "Usia: " usia)
        (println "Jenis kelamin: " kel))
;; Nama:  Dina
;; Usia:  21
;; Jenis kelamin:  :perempuan
;; => nil
```
Selain cara di atas, ada cara alternatif yang lebih singkat untuk mengakses nilai dari maps. Apabila kita tahu keyword apa saja yang ada di suatu maps, kita bisa memakai `:keys` untuk mengakses nilai di dalamnya. Dengan `:keys`, keyword yang ada di dalam maps ditempatkan di dalam sebuah vector dan dihilangkan tanda titik dua-nya `:` seperti contoh di bawah.

```clojure 
;; perhatikan bahwa symbol di dalam vector :keys tidak harus berurutan.
;; selain itu, tanda titik dua : dihilangkan dari tiap keyword.
user> (let [{:keys [jenis-kelamin nama umur]} bio-dina]
        (println "Nama: " nama)
        (println "Usia: " umur)
        (println "Jenis kelamin: " jenis-kelamin))
;; Nama:  Dina
;; Usia:  21
;; Jenis kelamin:  :perempuan
;; => nil
```

Untuk mendestruktur maps bersarang seperti `nilai-dina`, kita bisa menggunakan kombinasi destrukturisasi di atas seperti contoh berikut. Perlu diingat bahwa `:hasil-ujian` terasosiasi dengan sebuah maps yang berisi nilai, sehingga kita bisa memakai `:keys` untuk mengakses tiap nilai.

```clojure 
user> (let [{nama :nama
             usia :umur
             {:keys [ujian-2 ujian-4]} :hasil-ujian} nilai-dina]
        (println "Name: " nama)
        (println "Usia: " usia)
        (println "Nilai ujian 2 dan ujian 4 adalah " ujian-2 "dan" ujian-4))
;; Name:  Dina
;; Usia:  21
;; Nilai ujian 2 dan ujian 4 adalah  66 dan 93
;; => nil
```

Setelah mendestruktur koleksi dengan `let`, kita bisa menerapkan teknik tersebut terhadap parameter fungsi. Dengan destrukturisasi parameter, kita bisa mengakses elemen suatu koleksi secara lebih ringkas. Berikut adalah contoh fungsi dengan destrukturisasi parameter vector (hasil konversi dari contoh `let` di atas). 

```clojure 
user> (defn fn-tanpa-destruk [data]
        (println "Parameter tanpa destrukturisasi:" data))
;; => #'user/fn-tanpa-destruk

user> (defn fn-destruk-vec [[nama usia jkelamin]]
        (println "Nama:" nama)
        (println "Usia:" usia)
        (println "Jenis kelamin:" jkelamin))
;; => #'user/fn-destruk-vec

user> (defn fn-destruk-vec-sarang [[nama usia _ [_ u2 u3 _]]]
        (println "Nama:" nama)
        (println "Usia:" usia)
        (println "Hasil ujian 2 dan 3 adalah:" u2 "dan" u3))
;; => #'user/fn-destruk-vec-sarang
```
Perhatikan bahwa secara struktur, definisi fungsi dan contoh `let` di atas sangat mirip. Perbedaannya hanya di nama fungsi dan vector untuk *binding* yang tidak memiliki nilai yang akan di-*bind* (`bio-budi`, `nilai-budi`, dsb).

Berikut adalah contoh saat kita memakai fungsi di atas untuk memproses `bio-budi` dan `nilai-budi`. Perhatikan bahwa fungsi `fn-tanpa-destruk` memiliki 1 parameter skalar, yang berarti ia bisa menerima berbagai macam struktur koleksi (ataupun skalar) tanpa harus melakukan destrukturisasi.

```clojure 
user> (fn-tanpa-destruk bio-budi)
;; Parameter tanpa destrukturisasi: [Budi 20 :laki-laki]
;; => nil

user> (fn-tanpa-destruk bio-dina)
;; Parameter tanpa destrukturisasi: {:nama Dina, :umur 21, :jenis-kelamin :perempuan}
;; => nil

user> (fn-destruk-vec bio-budi)
;; Nama: Budi
;; Usia: 20
;; Jenis kelamin: :laki-laki
;; => nil

user> (fn-destruk-vec-sarang nilai-budi)
;; Nama: Budi
;; Usia: 20
;; Hasil ujian 2 dan 3 adalah: 85 dan 80
;; => nil
```

Misalkan data yang kita punya adalah dalam bentuk list (bukan vector), fungsi yang sudah kita buat di atas masih bisa memprosesnya juga.

```clojure 
;; buat list terlebih dahulu
user> (def list-budi '("Budi" 20 :laki-laki))
;; => #'user/list-budi

user> (def list-bersarang-budi '("Budi" 20 :laki-laki (70 85 80 91)))
;; => #'user/list-bersarang-budi

user> (fn-tanpa-destruk list-budi)
;; Parameter tanpa destrukturisasi: (Budi 20 :laki-laki)
;; => nil

user> (fn-destruk-vec list-budi)
;; Nama: Budi
;; Usia: 20
;; Jenis kelamin: :laki-laki
;; => nil

user> (fn-destruk-vec-sarang list-bersarang-budi)
;; Nama: Budi
;; Usia: 20
;; Hasil ujian 2 dan 3 adalah: 85 dan 80
;; => nil
```

Selanjutnya, mari kita buat fungsi yang dapat melakukan destrukturisasi terhadap parameter dalam bentuk maps. Perhatikan pula bahwa strukturnya tidak jauh berbeda dengan contoh `let` di atas.

```clojure 
user> (defn fn-destruk-maps [{nama :nama
                              usia :umur
                              kel :jenis-kelamin}]
        (println "Nama: " nama)
        (println "Usia: " usia)
        (println "Jenis kelamin: " kel))
;; => #'user/fn-destruk-maps

user> (defn fn-destruk-maps-keys [{:keys [jenis-kelamin nama umur]}]
        (println "Nama: " nama)
        (println "Usia: " umur)
        (println "Jenis kelamin: " jenis-kelamin))
;; => #'user/fn-destruk-maps-keys

user> (defn fn-destruk-maps-sarang [{nama :nama
                                     usia :umur
                                     {:keys [ujian-2 ujian-4]} :hasil-ujian}]
        (println "Name: " nama)
        (println "Usia: " usia)
        (println "Nilai ujian 2 dan ujian 4 adalah " ujian-2 "dan" ujian-4))
;; => #'user/fn-destruk-maps-sarang
```

Mari kita coba pakai fungsi di atas untuk memproses maps `bio-dina` dan `nilai-dina`.

```clojure 
user> (fn-destruk-maps bio-dina)
;; Nama:  Dina
;; Usia:  21
;; Jenis kelamin:  :perempuan
;; => nil

user> (fn-destruk-maps-keys bio-dina)
;; Nama:  Dina
;; Usia:  21
;; Jenis kelamin:  :perempuan
;; => nil

user> (fn-destruk-maps-sarang nilai-dina)
;; Name:  Dina
;; Usia:  21
;; Nilai ujian 2 dan ujian 4 adalah  66 dan 93
;; => nil
```

Dari contoh-contoh di atas, kita bisa melihat bahwa fungsi yang sudah kita buat bisa memproses argumen dalam bentuk koleksi.

## Penutup
Di bagian ini kita sudah belajar tentang *multi-arity* dan juga destrukturisasi parameter. Dengan *multi-arity*, kita bisa mendefinisikan sebuah fungsi yang bisa menerima jumlah argumen yang berbeda. Biasanya digunakan untuk membuat fungsi yang memiliki nilai default. Destrukturisasi parameter membantu kita untuk mengakses elemen dari suatu argumen secara ringkas. Kita bisa memilih elemen mana saja yang kita perlukan di badan fungsi dan mana saja yang bisa diabaikan.
