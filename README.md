# Membangun Layanan Web Yang Tenang
## Apa yang Akan Anda Bangun
Anda akan membangun layanan yang akan menerima permintaan HTTP GET di http://localhost:8012/greeting. Ini akan merespons dengan representasi JSON dari salam, seperti yang ditunjukkan oleh daftar berikut:<br>
```javascript
{"id":1,"content":"Hello, World!"}
```
Anda dapat menyesuaikan salam dengan parameter opsional name dalam string kueri, seperti yang ditunjukkan oleh daftar berikut:<br>
```javascript
http://localhost:8012/greeting?name=User
```
Nilai name parameter menggantikan nilai default World dan tercermin dalam respons, seperti yang ditunjukkan oleh daftar berikut:<br>
```javascript
{"id":1,"content":"Hello, User!"}
```
## Apa Yang Dibutuhkan
* Sekitar 15 menit
* Editor teks atau IDE favorit
* JDK 1.8 atau lebih baru
* Gradle 4+ atau Maven 3.2+
* Anda juga dapat mengimpor kode langsung ke IDE Anda:
  - Spring Tool Suite (STS)
  - IntelliJ IDEA
## Langkah-Langkah
<b>Langkah 1: Dimulai dengan Spring Initializer</b><br>

1. Arahkan ke https://start.spring.io. Layanan ini menarik semua dependensi yang Anda perlukan untuk aplikasi dan melakukan sebagian besar penyiapan untuk Anda.
2. Pilih Gradle atau Maven dan bahasa yang ingin Anda gunakan. Panduan ini mengasumsikan bahwa Anda memilih Java.
3. Klik <b>Dependencies</b> dan pilih <b>Spring Web</b>.
4. Klik <b>Generate</b>.
5. Unduh file ZIP yang dihasilkan, yang merupakan arsip aplikasi web yang dikonfigurasi dengan pilihan Anda.<br>
![rest](https://user-images.githubusercontent.com/113502513/192326668-ce58dc89-6853-467b-be91-cf687a800d65.JPG)<br>

<b>Langkah 2: Buat Kelas Representasi Sumber Daya</b><br>

Layanan akan menangani GET permintaan untuk /greeting, secara opsional dengan name parameter dalam string kueri. Permintaan GET harus mengembalikan 200 OK respons dengan JSON di badan yang mewakili greeting. Itu harus menyerupai output berikut:<br>
```javascript
{
    "id": 1,
    "content": "Hello, World!"
}
```
Bidang id adalah pengidentifikasi unik untuk greeting, dan contentmerupakan representasi tekstual dari greeting.<br>

Untuk memodelkan representasi ucapan, buat kelas representasi sumber daya. Untuk melakukannya, sediakan objek Java lama biasa dengan bidang, konstruktor, dan pengakses untuk data id dan content, seperti yang ditunjukkan oleh daftar berikut (dari src/main/java/com/iqbal/restservice/Greeting.java):<br>
```javascript
package com.iqbal.restful.service;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```

<b>Langkah 3: Buat Pengontrol Sumber Daya</b><br>

Dalam pendekatan Spring untuk membangun layanan web RESTful, permintaan HTTP ditangani oleh pengontrol. Komponen-komponen ini diidentifikasi oleh @RestControlleranotasi, dan yang GreetingController ditampilkan dalam daftar berikut (dari src/main/java/com/iqbal/restservice/GreetingController.java) menangani GET permintaan /greeting dengan mengembalikan instance baru dari class Greeting:<br>
```javascript
package com.iqbal.restfulservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @GetMapping("/greeting")
    public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
        return new Greeting(counter.incrementAndGet(), String.format(template, name));
    }
}
```

<b>Langkah 4: Cara Menukar Port</b><br>

Pada projek kita, pergi ke <b>Other Sources</b> >> <b>src/main/resources</b> >> <b>default package</b> >> <b>application.properties</b>. Lalu buat server.port= *nomorPort* pada application.packages agar tidak terjadi bentrok antar port.<br>
![app](https://user-images.githubusercontent.com/113502513/192334974-15e93203-b217-4226-adb8-a40e0293a3bc.JPG)<br>

<b>Langkah 5: Uji Layanan</b><br>

Sekarang setelah layanan aktif, kunjungi http://localhost:8012/greeting, di mana Anda akan melihat:<br>
![greet](https://user-images.githubusercontent.com/113502513/192337115-6d77472b-8614-4a47-8943-c39dad80f053.JPG)<br>

Berikan name parameter string kueri dengan mengunjungi http://localhost:8011/greeting?name=User. Perhatikan bagaimana nilai content atribut berubah dari Hello, World! ke Hello, User!, seperti yang ditunjukkan daftar berikut:<br>
![greet1](https://user-images.githubusercontent.com/113502513/192337250-0359216b-6852-4224-9b87-7937a90ef0de.JPG)<br>
Perubahan ini menunjukkan bahwa @RequestParampengaturan dalam GreetingController bekerja seperti yang diharapkan. Parameter name telah diberi nilai default World tetapi dapat diganti secara eksplisit melalui string kueri.<br>

Perhatikan juga bagaimana id atribut telah berubah dari 1 menjadi 2. Ini membuktikan bahwa Anda bekerja melawan GreetingControllerinstance yang sama di beberapa permintaan dan counter bidangnya bertambah pada setiap panggilan seperti yang diharapkan.
