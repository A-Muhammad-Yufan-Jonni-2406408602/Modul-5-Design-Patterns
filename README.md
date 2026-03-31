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

1. Menurut pemahaman saya terhadap Observer Pattern, Subscriber biasanya didefinisikan sebagai interface (atau trait dalam Rust) agar sistem menjadi fleksibel, terutama jika terdapat banyak jenis subscriber dengan perilaku yang berbeda. Namun, dalam kasus BambangShop ini, jika semua subscriber memiliki struktur dan perilaku yang relatif sama, maka penggunaan satu Model struct saja sudah cukup. Trait baru akan lebih dibutuhkan jika ke depannya ada variasi subscriber dengan implementasi yang berbeda-beda, sehingga desain menjadi lebih scalable dan mengikuti prinsip polymorphism dalam OOP.

2. Terkait dengan penggunaan struktur data, id pada Program dan url pada Subscriber bersifat unik. Jika hanya menggunakan Vec (list), maka pengecekan keunikan akan menjadi kurang efisien karena harus melakukan pencarian secara linear setiap kali menambah data baru. Oleh karena itu, penggunaan struktur seperti map atau dictionary (misalnya DashMap) lebih tepat karena memungkinkan akses dan pengecekan data secara lebih cepat (konstan), serta secara natural mendukung key yang unik.

3. Dalam konteks thread safety di Rust, penggunaan DashMap memberikan keuntungan karena sudah mendukung akses paralel secara aman tanpa perlu mengatur sinkronisasi secara manual. Jika hanya menggunakan Singleton pattern, memang kita bisa memastikan hanya ada satu instance data, tetapi Singleton tidak secara otomatis menjamin thread safety. Oleh karena itu, dalam kasus ini, penggunaan DashMap tetap diperlukan karena lebih sesuai untuk menangani kebutuhan concurrent access sekaligus menjaga keamanan data dalam lingkungan multithreaded.

#### Reflection Publisher-2

1. Menurut pemahaman saya tentang prinsip desain, pemisahan antara Model, Service, dan Repository diperlukan untuk menjaga **separation of concerns** agar setiap bagian memiliki tanggung jawab yang jelas. Pada pola MVC klasik, Model memang mencakup data dan business logic, tetapi dalam aplikasi yang lebih kompleks, hal ini dapat membuat Model menjadi terlalu besar dan sulit dirawat. Dengan memisahkan Repository sebagai pengelola akses data (misalnya database atau penyimpanan) dan Service sebagai tempat business logic, kode menjadi lebih modular, mudah diuji, dan lebih fleksibel untuk dikembangkan di masa depan.

2. Jika hanya menggunakan Model tanpa memisahkan Service dan Repository, maka setiap Model seperti Program, Subscriber, dan Notification akan menangani terlalu banyak hal sekaligus, mulai dari penyimpanan data, logika bisnis, hingga interaksi dengan model lain. Hal ini akan meningkatkan kompleksitas kode secara signifikan karena setiap perubahan pada satu bagian dapat berdampak ke banyak bagian lain. Selain itu, akan lebih sulit untuk melakukan testing dan maintenance karena tidak ada batasan yang jelas antara tanggung jawab masing-masing komponen.

#### Reflection Publisher-3

1. Pada tutorial ini, kita menggunakan **Observer Pattern dengan pendekatan Push model**, di mana publisher secara langsung mengirimkan data (payload notifikasi) ke setiap subscriber. Hal ini terlihat dari bagaimana `NotificationService` menyiapkan data notifikasi, lalu memanggil method `update()` pada setiap Subscriber dan mengirimkan informasi produk melalui HTTP POST. Dengan pendekatan ini, subscriber tidak perlu meminta data secara manual karena semua informasi sudah dikirimkan oleh publisher.

2. Jika dibandingkan dengan Pull model, di mana subscriber harus mengambil data sendiri dari publisher, pendekatan tersebut memiliki beberapa kelebihan dan kekurangan. Kelebihannya adalah subscriber memiliki kontrol lebih besar terhadap data apa yang ingin diambil, sehingga bisa lebih fleksibel dan mengurangi pengiriman data yang tidak diperlukan. Namun, kekurangannya adalah kompleksitas yang lebih tinggi karena subscriber harus melakukan request tambahan, serta potensi keterlambatan dalam mendapatkan data karena tidak langsung dikirim oleh publisher. Dalam kasus BambangShop, Pull model bisa membuat sistem menjadi lebih lambat dan tidak efisien karena setiap subscriber harus melakukan request sendiri setelah suatu event terjadi.

3. Jika program tidak menggunakan multi-threading dalam proses notifikasi, maka pengiriman notifikasi ke subscriber akan dilakukan secara berurutan (synchronous). Hal ini dapat menyebabkan performa menjadi lebih lambat, terutama jika jumlah subscriber banyak atau ada subscriber yang lambat merespons request. Akibatnya, seluruh proses notifikasi bisa terhambat hanya karena satu subscriber, sehingga mengurangi efisiensi sistem secara keseluruhan. Dengan menggunakan multi-threading, notifikasi dapat dikirim secara paralel sehingga lebih cepat dan tidak saling menghambat.
