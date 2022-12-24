+++
title = "Bagian 1: Mengenal Clojure"
date = "2022-12-24T18:29:58+07:00"

#
# description is optional
#
description = "Tutorial Belajar Clojure Bagian 1: Berkenalan dengan Clojure"

tags = ["Clojure", "tutorial"]
+++


## Apa itu clojure

![Logo Clojure](/img/tutorial-clojure/bab-1/clojure-logo.png)

Clojure adalah bahasa pemrograman dinamis multiguna dan termasuk dalam keluarga bahasa [Lisp](https://id.wikipedia.org/wiki/Lisp_(bahasa_pemrograman)). Ia adalah *hosted language*, yang artinya ia berjalan di platform lain. Dalam hal ini, meskipun Clojure berbasis Lisp, ia dapat berjalan di Java Virtual Machine (JVM), [JavaScript runtime](https://clojurescript.org/), dan [Common Language Runtime (CLR)](https://clojure.org/about/clojureclr). Jadi, kode clojure yang kita tulis akan dikompilasi ke bahasa Java, JavaScript, atau C# sebelum dijalankan di platform-platform tersebut.

Clojure pertama kali dikembangkan oleh [Rick Hickey](https://en.wikipedia.org/wiki/Rich_Hickey) di tahun 2005 dan dirilis pada Oktober 2007. Namun, Lisp yang menjadi basis dari Clojure sudah ada sejak 1958 (Lisp adalah bahasa pemrograman tertua kedua setelah Fortran). Clojure adalah bahasa pemrograman fungsional yang bisa dipakai untuk berbagai macam tujuan. Rick Hickey mendesain Clojure agar menjadi bahasa pemrograman yang pragmatis, sederhana, dan aman. Clojure dikembangkan untuk memudahkan developer dalam membuat software, terutama dalam skala besar. Selain itu, ia juga dirancang untuk memudahkan konkurensi. Beberapa alasan kenapa Clojure dibuat dapat di lihat [di sini](https://clojure.org/about/rationale).

## Fitur-fitur

### Dynamic Development
Clojure memiliki lingkungan REPL (Read-Eval-Print-Loop) yang kita bisa gunakan sebagai antaramuka interaktif saat kita menulis program Clojure. REPL ini mirip seperti interpreter di Python, JavaScript, Ruby, dll. Jadi, selain bisa dikompilasi, kita juga bisa menulis kode kita di REPL, yang nantinya akan dikompilasi ke JVM Bytecode dan dieksekusi saat itu juga secara otomatis. REPL sangat membantu kita untuk memeriksa bagaimana suatu program bekerja. Selain itu, ia juga sangat bermanfaat saat sedang bereksperimen dengan suatu program tanpa harus melalui siklus *compile-run* terus menerus. Saat kita sudah yakin program kita berjalan sesuai ekspektasi, kita bisa masukkan kode yang kita tulis di REPL ke source code. 

### Functional Programming
Clojure adalah bahasa [pemrograman fungsional](https://en.wikipedia.org/wiki/Functional_programming). Fitur-fitur bahasa pemrograman fungsional yang cukup menonjol antara lain *immutable data*, *first-class function*, dan *recursive loop*. ***Immutable data*** artinya kita tidak bisa mengubah nilai atau isi dari suatu variabel. Yang bisa dilakukan adalah **rebinding**, artinya kita mengalokasikan ruang memori baru lalu memerintahkan si variable itu untuk menunjuk ke lokasi memori dari nilai yang baru dibuat. Nilai yang lama masih ada di memori dan akan dibersihkan oleh garbage collector kalau sudah tidak diperlukan. ***First class function*** artinya fungsi dapat diperlakukan layaknya tipe data umumnya seperti integer, string, dll. Kita bisa membuat fungsi yang *menerima fungsi* lain sebagai argumen atau *memberikan fungsi sebagai nilai kembalian* (alih-alih tipe data lainnya). Terakhir, perulangan di Clojure bersifat ***recursive***. Artinya, fungsi akan memanggil dirinya sendiri untuk melakukan tugas selanjutnya. Ini cukup berbeda dengan perulangan `for` atau `while` yang lebih bersifat enumerasi (dari sekian sampai sekian, lakukan ini dan itu).

Meskipun berparadigma fungsional, Clojure cukup pragmatis karena ia mengakomodasi banyak konsep yang ada di paradigma lain. Oleh karena itu, seharusnya sintaksis dan semantik Clojure tidak terlalu asing kalau kita sudah terbiasa dengan pemrograman.

### Lisp
Clojure didesain untuk menggunakan sintaksis bahasa Lisp. Fitur yang sangat menonjol dari bahasa Lisp adalah **notasi prefiks** dan penggunaan **tanda kurung** untuk membatasi lingkup kode. Notasi prefiks berarti fungsi yang kita panggil *selalu* ada di bagian depan. Sebagai contoh, kalau ingin menjumlahkan 4 bilangan bulat, kita bisa menuliskannya dengan `1 + 2 + 3 + 4`. Di Lisp (juga Clojure), kita harus menulisnya dengan `(+ 1 2 3 4)`. Perhatikan penggunaan tanda kurungnya. Contoh lain, untuk operasi `5 * 7 + 2 - 4`, kita harus menuliskan `(- 4 (+ 2 (* 5 7)))`. Kita harus menentukan urutan operasi yang harus dilakukan, alih-alih harus mengikuti urutan yang sudah ditetapkan.

Bagi pemula atau yang belum terbiasa, membaca dan memahami sitaksis Lisp perlu waktu yang cukup lama. Namun kalau sudah terbiasa, aturan sintaksis ini sebenarnya sangat sederhana dan tanda kurung ini sebenarnya punya peran sangat penting.

### Hosted Language
Seperti yang sudah saya sebutkan sebelumnya, Clojure adalah *hosted language* yang dapat berjalan di beberapa platform. Ini berarti kita cukup belajar satu bahasa pemrograman saja dan program yang kita buat dapat berjalan di tiga platform yang berbeda. Tentu saja ada perbedaan sintaksis yang perlu disesuaikan dengan platform target, namun biasanya tidak terlalu banyak. Clojure dapat memanfaatkan ekosistem yang sudah matang di tiap platform. Jadi, kita bisa memakai library dari Java, JavaScript, atau C# yang sudah tersedia tanpa harus menulis ulang.

### Concurrent Programming
Clojure didesain sejak awal untuk mendukung konkurensi dengan memanfaatkan CPU core semaksimal mungkin. Clojure memudahkan kita untuk membuat program yang konkuren karena ada banyak primitive yang sudah siap pakai. Selain itu, Clojure juga memiliki mutable object yang bisa digunakan khusus untuk konkurensi seperti `atom`, `agent`, dan `refs`.

## Kenapa harus belajar Clojure?
Setelah kita tahu beberapa fiturnya, selanjutnya adalah pertanyaan "kenapa kita harus belajar Clojure?". Barangkali sebagian dari kalian sudah punya jawabannya sebelum datang ke sini. Sebagian mungkin masih bingung karena ini pertama kalinya mendengar Clojure. Bagi yang masih bingung, izinkan saya memberikan beberapa alasan yang semoga cukup meyakinkan dan bisa diterima.

### Bahasa pemrograman dengan bayaran tertinggi
Berdasarkan **Stack Overflow Developer Survey [2021](https://insights.stackoverflow.com/survey/2021#technology-top-paying-technologies)** dan [**2022**](https://survey.stackoverflow.co/2022/#technology-top-paying-technologies), Clojure adalah bahasa pemrograman dengan bayaran tertinggi. 

**Gaji median 2022** 
![Gaji median 2022](/img/tutorial-clojure/bab-1/salary-2022.png) 

**Gaji median 2021** 
![Gaji median 2021](/img/tutorial-clojure/bab-1/salary-2021.png) 

Pendapatan median tahunan untuk Clojure developer ada di kisaran USD 100K (~ IDR 1.5 Milyar), atau sekitar USD 8333 (~ IDR 130 Juta) per bulan. Perusahaan yang memakai Clojure kebanyakan ada di bidang keuangan dan konsultan. Gaji yang tinggi ini bisa disebabkan karena Clojure memudahkan developer untuk menyelesaikan dan merawat program yang dibuat. Tapi ada sedikit catatan tentang survey di atas: ada kemungkinan bias terhadap developer yang sudah berpengalaman sehingga nilainya sangat tinggi. Selain itu, mayoritas responden survey berasal dari AS dan Eropa. Meskipun begitu, dari pengalaman saya, bila dibandingkan dengan posisi developer bahasa pemrograman lain, tawaran gaji untuk Clojure developer yang masih entry level memang di atas rata-rata. Apabila kita bisa menguasai Clojure, kita berpeluang untuk mendapatkan pekerjaan dengan gaji yang jauh di atas rata-rata.

### Masuk dalam kategori most loved languages

**Bahasa pemrograman paling disukai 2022** 
![2022](/img/tutorial-clojure/bab-1/loved-2022.png) 

**Bahasa pemrograman paling disukai 2021** 
![2021](/img/tutorial-clojure/bab-1/loved-2022.png) 

Masih terkait dengan **Stack Overflow Developer Survey [2021](https://insights.stackoverflow.com/survey/2021#technology-most-loved-dreaded-and-wanted)** dan [**2022**](https://survey.stackoverflow.co/2022/#technology-most-loved-dreaded-and-wanted), Clojure termasuk dalam jajaran bahasa pemrograman yang paling disukai oleh developer. Ini karena Clojure menawarkan pengalaman membuat program yang cukup nyaman dan membuat developer lebih produktif. 

### Lisp
Saya masukkan Lisp sebagai salah satu alasan untuk belajar Clojure karena sintaksisnya sangat sederhana dan mudah dipahami. Struktur bahasanya juga hampir selalu sama. Ada alasan kenapa masih banyak orang yang suka dengan sintaksis Lisp meskipun ia termasuk "kuno". Memang butuh waktu untuk membiasakan dan ini biasanya cukup lama. Saat sudah melewati fase "(dibuat-pusing-oleh-tanda-kurung)", sepertinya kalian akan sadar kenapa Lisp ini elegan dan *powerful* untuk mengekspresikan ide kita ke dalam program. Paling tidak itu yang saya rasakan.

### Satu bahasa untuk berbagai platform
Seperti yang kita tahu, program sekarang ini makin kompleks dan dibuat dengan berbagai macam bahasa pemrograman. Tiap bahasa memiliki aturan sintaksis yang berbeda dan seringkali kita "dimarahi" compiler atau interpreter karena ada `}` atau `;` yang ketinggalan. Dengan Clojure (dan turunnanya), kita bisa menulis program dalam satu bahasa dan program tersebut dapat berjalan di banyak platform. Sebagai contoh, saat kita membuat sebuah website, kita bisa membangun backend dengan Clojure yang berjalan di JVM, lalu membuat frontend dengan ClojureScript yang dapat berjalan di browser. Meskipun ada sedikit penyesuaian sintaksis, website tersebut bisa dibilang dibangun dengan satu bahasa pemrograman, yaitu Clojure. Dengan begini, kita bisa lebih fokus terhadap *business* atau *application logic* dari program kita, alih-alih disibukkan dengan urusan sintaksis.

## Kekurangan Clojure
Setelah menyebut alasan kenapa harus belajar Clojure, rasanya tidak adil kalau saya tidak menyebut kekurangannya. Jadi, berikut adalah daftar kekurangan Clojure berdasarkan pengalaman saya:

### Startup time JVM yang lambat
Karena berjalan di JVM, Clojure juga punya startup time yang cukup lama. Contoh yang sering saya alami adalah saat memulai REPL. Di laptop saya, biasanya ia butuh sekitar 5 detik untuk berjalan. 

### Sintaksis Lisp
Menurut saya, selain menjadi kelebihan Clojure, sintaksis Lisp ini juga bisa menjadi kekurangannya, terutama untuk pemula. Lisp bisa membuat orang ***ilfeel*** dan tidak mau belajar lagi. Saya sendiri sempat menyerah di tengah jalan karena tidak tahan dengan banyaknya tanda kurung di sana-sini. Tapi kekurangan ini bisa diatasi kalau kita memakai IDE yang punya fitur semacam bracket matching. 

### Learning curve yang curam
Perlu diakui bahwa belajar bahasa pemrograman baru seperti Clojure cukup susah **di awal**. Saya sebut **di awal** karena memang di masa inilah kita akan menemui banyak hal-hal baru dan asing. Perlu waktu dan percobaan berkali-kali untuk memahaminya. Selain itu, saat kita belajar Clojure, kita tidak hanya belajar bahasanya saja, tapi juga environment untuk development. Bagi yang tidak punya environment yang cocok untuk menulis Clojure, kemungkinan besar akan balik kanan juga. Di tutorial ini, selain belajar bahasa, saya juga akan sedikit menunjukan cara membangun environment Clojure dan pemakaiannya untuk memudahkan proses belajar.

### Kurang populer dan tidak banyak lowongan kerja
Clojure bukan bahasa yang populer dan tidak banyak perusahaan yang memakainya, terutama di Indonesia. Popularitas Clojure sendiri secara global menurut saya sedang naik di beberapa tahun belakangan ini, meskipun sangat lambat. Selain itu, lowongan kerja sebagai Clojure developer bisa dibilang tidak ada di Indonesia. Kalau ingin mencari posisi Clojure developer untuk saat ini, kebanyakan ada di AS dan Eropa. 

## Siap mulai?
Setelah berkenalan, kalian siap untuk mulai belajar Clojure? Di tulisan berikutnya, saya akan menjelaskan cara instalasi dan membangun environment Clojure. Mari kita mulai!

