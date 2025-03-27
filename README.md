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

#### Reflection Publisher-1

1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Dalam kasus BambangShop, menggunakan struct Subscriber tanpa trait/interface sudah cukup karena semua subscriber memiliki perilaku identik dan hanya perlu menerima HTTP POST ke endpoint `/receive` dengan format notifikasi yang seragam. Meskipun pola Observer pada umumnya menggunakan interface untuk fleksibilitas berbagai tipe observer, saat ini kompleksitas tambahan trait tidak diperlukan di sini karena  perilaku subscriber masih seragam dan sistem notifikasinya masih sederhana yang hanya mengandalkan satu mekanisme pengiriman.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Pada konteks sekarang, menggunakan DashMap lebih tepat daripada Vec karena kita butuh unique constraint untuk id (pada Product) dan url (pada Subscriber). Selain itu, dengan menggunakan DashMap (hashmap), kita bisa mendapatkan kompleksitas O(1) untuk operasi pencarian/update, sedangkan Vec butuh O(n) untuk mengecek duplikat. Lalu, Dengan DashMap, kita secara natural mencegah duplikasi key (id/url) karena struktur data ini tidak mengizinkan key yang sama. Meskipun Vec bisa dipaksa untuk mengecek unik (dengan iterasi tiap insert), ini tidak efisien dan rawan human error. Oleh karena itu, saat ini lebih baik menggunakan DashMap.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Untuk kasus list subscriber, DashMap tetap lebih baik dibanding singleton biasa karena sudah menyediakan thread-safety secara otomatis untuk operasi konkuren. Selain itu, singleton pattern biasa tidak cukup karena tidak otomatis melindungi data dari race condition ketika diakses bersamaan. Dengan DashMap, kita bisa yakin data subscriber tetap konsisten meskipun ada banyak request masuk secara paralel. Solusi singleton manual akan membutuhkan kode tambahan yang kompleks untuk mencapai keamanan yang sama. Oleh sebab itu, DashMap masih lebih praktis dibandingkan singleton pattern.

#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Memisahkan Service dan Repository dari Model dilakukan untuk menerapkan prinsip Separation of Concerns (SoC) dan memudahkan pengelolaan kode. Model hanya fokus menyimpan struktur data, Repository khusus menangani penyimpanan/akses data (seperti database), sedangkan Service mengatur logika bisnis yang kompleks. Hal ini dilakukan agar membuat kode lebih modular, mudah di-test, dan fleksibel jika ada perubahan infrastruktur (misalnya kayak ganti database). Selain itu, Logika bisnis di Service juga bisa diubah tanpa mempengaruhi cara data disimpan. Dengan pembagian tanggung jawab yang jelas, kode jadi lebih bersih dan scalable.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika hanya menggunakan Model tanpa pemisahan Service dan Repository, kode bakal menjadi kacau karena setiap Model harus menangani terlalu banyak tanggung jawab sekaligus seperti mulai dari penyimpanan data, logika bisnis, hingga komunikasi antar Model. Dimana ada kemungkinan Product perlu mengurus subscriber, Subscriber harus paham cara membuat Notification, dan Notification perlu tahu cara menyimpan diri ke database, menciptakan dependensi berantai yang susah ditrack. Akibatnya, perubahan kecil seperti mengubah cara penyimpanan data bisa merusak seluruh alur notifikasi, testing menjadi susah karena semua logika menyatu, dan kode berkembang menjadi sangat kompleks dan rumit dipahami seiring penambahan fitur. Oleh karena itu, menurut saya pemisahan layer Service dan Repository justru diperlukan dalam membantu menyederhanakan kompleksitas ini dengan memberi batasan tanggung jawab yang jelas untuk setiap komponennya

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Postman cukup membantu saya dalam menguji API BambangShop karena memungkinkan pengiriman berbagai jenis request (GET/POST/DELETE) dengan mudah, termasuk mengatur header dan body JSON. Lalu, ada fitur collection yang tersusun rapi sehingga mempermudah testing berulang untuk endpoint seperti subscribe, create product, dan receive notification. Untuk fitur lain yang dapat membantu proyek kelompok, saya belum eksplore lebih lanjut. Namun, di Postman terdapat fitur mock server dan dokumentasi otomatis yang mungkin akan berguna untuk proyek tim yang lebih kompleks.

#### Reflection Publisher-3
