+++
title = "Bagian 9-c: Fungsi Variadic"
date = "2023-06-19T14:54:04+07:00"

#
# description is optional
#
# description = "An optional description for SEO. If not provided, an automatically created summary will be used."

tags = ["clojure","tutorial",]
+++

Di bagian 9-b kita sudah mengenal fungsi dengan *multi-arity* yang dapat menerima beberapa jumlah argumen yang berbeda. Dengan *multi-arity*, kita bisa mendefinisikan perilaku fungsi berdasarkan tiap *arity*. Namun begitu, sangat tidak efisien jika kita harus mendefinisikan tiap *arity*. Misalkan kita harus membuat fungsi yang dapat menerima 1 sampai 10 argumen, maukah kita menulis tiap definisinya satu per satu? Kalaupun mau, memakai *multi-arity* di sini sangat tidak efisien dan rentan error karena bisa jadi ada banyak salah ketik di tiap definisinya.

Untungnya di Clojure ada yang namanya *variadic functions*. Dari kata *variadic* paling tidak kita bisa sedikit punya gambaran karena ia mirip dengan kata *vary*/*variable*. ***Variadic function*** adalah fungsi yang dapat menerima **jumlah argumen yang tak terbatas**. *Multi-arity function* dapat menerima beberapa argumen sesuai dengan yang sudah didefinisikan, sedangkan *variadic function* bisa menerima berapapun argumen meskipun tidak didefinisikan secara eksplisit. 

Salah satu contoh sederhana *variadic function* di Clojure adalah fungsi untuk aritmatika seperti `+`, `-`, `/`, dan `*`. Mereka bisa melakukan operasi aritmatika terhadap berapapun jumlah bilangan yang kita inginkan (tidak terbatas hanya 2 bilangan). Mari kita cek dokumentasi dari salah satu fungsi aritmatika di atas, yaitu `+`.

```clojure 
user=> (doc +)
;; -------------------------
;; clojure.core/+
;; ([] [x] [x y] [x y & more])
;;   Returns the sum of nums. (+) returns 0. Does not auto-promote
;;   longs, will throw on overflow. See also: +'
;; => nil
```

Jika dilihat dari output dokumentasi di atas, fungsi `+` memiliki 4 *arity* yang berbeda: selain bisa menerima 0 (`[]`), 1 (`[x]`), dan 2 argumen (`[x y]`), ia juga bisa meng-*handle* `[x y & more]`. Maksud dari `[x y & more]` adalah fungsi `+` bisa menerima lebih dari 2 argumen. Tanda ampersan `&` menandai bahwa parameter selanjutnya (`more`) adalah dalam bentuk list. Nama parameter list tersebut bebas dan tidak harus `more`, yang terpenting ia terletak setelah tanda ampersan `&`. Parameter list ini dipakai untuk menyimpan argumen yang diterima fungsi `+` dan tidak ada batasan jumlah. Jadi, berdasarkan dokumentasi di atas, fungsi `+` adalah fungsi *variadic* **sekaligus** `multi-arity`.

Seringkali *variadic function* digunakan untuk membuat fungsi yang menerima argumen opsional. Argumen opsional inilah yang disimpan di dalam parameter list tersebut. Perlu diingat bahwa di dalam definisi fungsi, argumen opsional harus diletakkan di paling belakang. Mari kita coba buat sebuah fungsi untuk mendemonstrasikan *variadic function* agar lebih jelas. 

```clojure 
;; parameter opsional (sisa) harus diletakkan setelah parameter utama
;; dan harus didahului dengan '&'
user> (defn test-variadic [utama & sisa]
        (println "Argumen utama adalah" utama)
        (println "Argumen opsional adalah" sisa))
;; => #'user/test-variadic

;; error karena parameter 'utama' tidak memiliki nilai
user> (test-variadic)
;; Execution error (ArityException) at user/eval8964 (REPL:72).
;; Wrong number of args (0) passed to: user/test-variadic

;; masih bisa dievaluasi karena paratemer 'sisa' bersifat opsional 
;; dan secara default bernilai nil
user> (test-variadic 100)
;; Argumen utama adalah 100
;; Argumen opsional adalah nil
;; => nil

;; argumen kedua dan seterusnya disimpan di dalam list 'sisa'
user> (test-variadic 100 567)
;; Argumen utama adalah 100
;; Argumen opsional adalah (567)
;; => nil

user> (test-variadic 100 567 980)
;; Argumen utama adalah 100
;; Argumen opsional adalah (567 980)
;; => nil
```
Karena argumen *variadic* disimpan di dalam list, kita tinggal memakai fungsi operasi list atau iterasi agar bisa memakainya di dalam badan fungsi.

Selanjutnya, mari kita kombinasikan *variadic function* dengan *multi-arity*. Dengan kombinasi ini, kita bisa membuat sebuah fungsi yang dapat menerima berapapun jumlah argumen yang kita beri. Contoh berikut ini secara struktur mirip dengan fungsi `+` di atas.

```clojure 
user> (defn variadic
        ([] (println "Tidak ada argumen yang diterima."))
        ([x] (println "Menerima 1 argumen, yaitu" x))
        ([x y] (println "Menerima 2 argumen, yaitu" x "dan" y))
        ([x y & lainnya] (println "Menerima lebih dari 2 argumen, yaitu" x "," y "dan" lainnya)))
;; => #'user/variadic

user> (variadic)
;; Tidak ada argumen yang diterima.
;; => nil

user> (variadic 10)
;; Menerima 1 argumen, yaitu 10
;; => nil

user> (variadic 10 20)
;; Menerima 2 argumen, yaitu 10 dan 20
;; => nil

user> (variadic 10 20 30)
;; Menerima lebih dari 2 argumen, yaitu 10 , 20 dan (30)
;; => nil

user> (variadic 10 20 30 40)
;; Menerima lebih dari 2 argumen, yaitu 10 , 20 dan (30 40)
;; => nil

user> (variadic 10 20 30 40 50 60 70 80)
;; Menerima lebih dari 2 argumen, yaitu 10 , 20 dan (30 40 50 60 70 80)
;; => nil
```

Di bagian ini kita sudah mengenal fungsi *variadic* yang memungkinkan kita untuk membuat sebuah fungsi yang dapat menerima berapapun jumlah argumen yang kita inginkan. Konsep *variadic* dan *multi-arity* membantu kita membuat fungsi dengan lebih fleksibel. Di bagian selanjutnya kita akan mengenal beberapa konsep yang berkaitan dengan pemrograman fungsional.
