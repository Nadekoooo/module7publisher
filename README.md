# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

# Refleksi Publisher-1

### 1. Apakah kita masih perlu menggunakan interface/trait untuk `Subscriber` dalam kasus BambangShop?

Dalam implementasi pola Observer tradisional, seperti yang dijelaskan dalam buku Head First Design Patterns, `Subscriber` biasanya didefinisikan sebagai interface (atau trait dalam Rust) untuk memungkinkan berbagai implementasi konkret. Abstraksi ini memungkinkan kita untuk membuat tipe subscriber yang berbeda dengan perilaku yang bervariasi, sehingga mendukung fleksibilitas dan ekstensibilitas sistem. Dalam konteks BambangShop, jika hanya ada satu jenis subscriber dengan perilaku yang tidak diharapkan berubah atau berkembang, satu model struct saja mungkin sudah cukup. Namun, dengan menggunakan trait, desain kita tetap terbuka untuk penambahan fitur atau perubahan di masa depan, jika memang dibutuhkan variasi perilaku subscriber. Oleh karena itu, meskipun penggunaan struct tunggal lebih sederhana, penggunaan interface (trait) memberikan lapisan abstraksi yang berguna untuk mengelola kompleksitas seiring pertumbuhan sistem.

### 2. Apakah menggunakan `Vec` sudah cukup atau penggunaan `DashMap` diperlukan untuk menjaga keunikan dan pencarian yang cepat?

Menjaga keunikan identifier seperti `id` pada Program dan `url` pada Subscriber sangat penting untuk integritas sistem. Meskipun `Vec` (daftar) bisa digunakan, penggunaan `Vec` biasanya memerlukan pencarian seluruh koleksi untuk memeriksa keunikan atau melakukan lookup, yang dapat menjadi tidak efisien saat jumlah data bertambah. Sebaliknya, struktur data berbasis map seperti `DashMap` memungkinkan akses langsung ke elemen melalui kunci, sehingga keunikan sudah dijamin secara otomatis dan pencarian, penyisipan, maupun penghapusan dapat dilakukan dengan lebih cepat. Efisiensi ini sangat penting dalam lingkungan yang membutuhkan performa tinggi dan memiliki akses data secara bersamaan. Dengan demikian, meskipun `Vec` mungkin memadai untuk dataset kecil, penggunaan `DashMap` merupakan pilihan yang lebih handal untuk mengelola daftar subscriber yang unik dan berkembang.

### 3. Apakah kita masih perlu `DashMap` untuk keamanan thread, atau kita bisa menerapkan pola Singleton saja?

Meskipun model kepemilikan (ownership) dan pengecekan compiler Rust memberikan dasar yang kuat untuk keamanan thread, ketika beberapa thread mengakses data bersama, diperlukan langkah tambahan untuk menghindari kondisi balapan (race condition). Pola Singleton memastikan hanya ada satu instance dari suatu struktur data, tetapi pola ini tidak secara inheren melindungi data dari modifikasi bersamaan atau masalah sinkronisasi. Dalam kasus kita, daftar subscriber bersama diakses oleh beberapa thread, sehingga penggunaan koleksi yang aman untuk thread seperti `DashMap` sangat diperlukan. `DashMap` dirancang untuk menangani akses konkuren dengan lancar tanpa perlu mekanisme kunci atau sinkronisasi manual. Oleh karena itu, meskipun kita dapat menerapkan pola Singleton untuk memastikan hanya ada satu instance, hal tersebut tidak menggantikan kebutuhan akan struktur data yang aman untuk thread, karena kedua pendekatan ini menangani aspek yang berbeda dalam permasalahan konkurensi.

## Refleksi Publisher-2

### 1. Mengapa kita perlu memisahkan "Service" dan "Repository" dari Model?

Dalam pola desain Model-View-Controller (MVC) klasik, Model bertanggung jawab atas penyimpanan data sekaligus logika bisnis. Namun, dalam praktik rekayasa perangkat lunak modern, memisahkan "Service" dan "Repository" dari Model adalah penerapan prinsip *Separation of Concerns* yang baik. `Repository` berfokus pada akses data (CRUD ke database atau penyimpanan lainnya), sedangkan `Service` menangani logika bisnis yang mengatur alur dan proses aplikasi. Dengan pemisahan ini, kode menjadi lebih modular, mudah diuji, dan mudah dipelihara. Hal ini juga memungkinkan pengembangan paralel oleh tim yang berbeda tanpa saling mengganggu, serta membuat sistem lebih fleksibel untuk perubahan teknologi penyimpanan atau logika bisnis.

### 2. Apa yang terjadi jika kita hanya menggunakan Model?

Jika kita hanya menggunakan Model untuk menangani semua tanggung jawab — termasuk logika bisnis dan penyimpanan data — maka kompleksitas setiap model akan meningkat drastis. Misalnya, jika `Program`, `Subscriber`, dan `Notification` saling berinteraksi langsung, maka setiap Model harus mengetahui terlalu banyak detail tentang Model lain. Ini akan menciptakan ketergantungan antar Model yang kuat (*tight coupling*), membuat perubahan kecil di satu bagian bisa berdampak besar pada bagian lain. Hal ini membuat pengujian lebih sulit, debugging jadi rumit, dan skalabilitas sistem menjadi terhambat. Selain itu, tanggung jawab Model menjadi terlalu besar, melanggar prinsip *Single Responsibility* dalam pemrograman berorientasi objek.

### 3. Apa manfaat menggunakan Postman? Fitur apa yang membantu?

Postman memungkinkan saya untuk mengirim HTTP request dengan lebih mudah dan simpel tanpa harus membuat UI terlebih dahulu. Hal ini sangat membantu saat menguji fungsi-fungsi seperti `subscribe` dan `unsubscribe` dalam proyek ini. Fitur seperti *Environment Variables*, *Collections*, dan *Pre-request Scripts* sangat berguna untuk menyusun skenario pengujian yang kompleks. Selain itu, fitur *Test Scripts* dan *Visualize* memberikan fleksibilitas dalam memvalidasi respons API secara otomatis. Postman jelas menjadi alat yang sangat bermanfaat tidak hanya dalam proyek kelompok ini, tapi juga untuk proyek rekayasa perangkat lunak nanti.

## Refleksi Publisher-3

### 1. Pola Observer mana yang digunakan: Push atau Pull?

Dalam kasus tutorial ini, kita menggunakan variasi **Push model** dari Observer Pattern. Artinya, sistem langsung mengirim (push) data notifikasi ke masing-masing subscriber begitu sebuah event terjadi, seperti produk dibuat, dihapus, atau dipromosikan. Subscriber tidak perlu melakukan permintaan secara aktif untuk mendapatkan informasi. Publisher akan langsung menghubungi semua subscriber yang sudah terdaftar saat ada perubahan data yang relevan. Hal ini membuat sistem menjadi lebih real-time dan efisien karena proses distribusi informasi berjalan otomatis tanpa inisiatif dari subscriber. Pola push sangat cocok digunakan dalam aplikasi seperti BambangShop yang ingin memberi update promosi atau status produk secara cepat ke seluruh pelanggan.

### 2. Apa kelebihan dan kekurangan jika kita menggunakan Pull model?

Jika kita menggunakan **Pull model**, maka subscriber harus secara aktif dan berkala mengecek ke publisher untuk mengetahui apakah ada informasi baru. Kelebihannya, subscriber bisa mengontrol kapan harus mengambil data, yang bisa berguna untuk mengatur beban jaringan atau jika update tidak perlu sering dilakukan. Namun, model ini juga membawa kekurangan besar seperti keterlambatan dalam menerima informasi karena tidak ada pemicu langsung dari publisher. Selain itu, perlu ada mekanisme tambahan seperti polling atau scheduler untuk mengecek data secara berkala, yang justru bisa menambah kompleksitas. Dalam konteks aplikasi BambangShop, hal ini tidak cocok karena akan membuat notifikasi promosi atau perubahan produk menjadi tidak langsung diterima dan pengalaman pengguna bisa terganggu.

### 3. Apa yang terjadi jika proses notifikasi tidak menggunakan multi-threading?

Jika proses notifikasi tidak dijalankan menggunakan multi-threading, maka notifikasi akan dikirim secara berurutan dan blocking, artinya satu notifikasi harus selesai terlebih dahulu sebelum lanjut ke notifikasi berikutnya. Ini bisa menyebabkan keterlambatan terutama jika jumlah subscriber banyak atau ada subscriber yang prosesnya lambat. Efeknya, publisher akan sibuk menangani satu per satu notifikasi dan tidak bisa melanjutkan proses lainnya dengan cepat. Hal ini dapat menurunkan performa sistem secara keseluruhan dan membuat aplikasi terasa lambat di sisi pengguna. Dengan multi-threading, setiap notifikasi bisa dikirim secara paralel, sehingga semua subscriber bisa mendapatkan update dalam waktu yang hampir bersamaan dan beban sistem menjadi lebih ringan dan efisien.

