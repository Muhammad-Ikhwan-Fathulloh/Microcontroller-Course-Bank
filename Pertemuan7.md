# Pertemuan 7: 🌐 Protokol Komunikasi: HTTP (REST API)

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami konsep protokol HTTP dan arsitektur REST API
2. Membedakan metode HTTP (GET, POST, PUT, DELETE)
3. Menggunakan ESP32 sebagai HTTP Client untuk mengambil dan mengirim data ke server
4. Memahami format data JSON dalam komunikasi IoT

---

## 📚 Materi Pokok

### 1. Konsep REST API

#### A. Apa itu HTTP?
HTTP (Hypertext Transfer Protocol) adalah protokol dasar komunikasi data di web. Dalam IoT, HTTP sering digunakan untuk menghubungkan perangkat (client) dengan web server.

#### B. Arsitektur REST
REST (Representational State Transfer) adalah gaya arsitektur piranti lunak yang menggunakan protokol HTTP untuk pertukaran data.

| Metode     | Fungsi             | Contoh dalam IoT                         |
| ---------- | ------------------ | ---------------------------------------- |
| **GET**    | Mengambil data     | Membaca status lampu dari server         |
| **POST**   | Mengirim data baru | Mengirim data suhu ke database           |
| **PUT**    | Memperbarui data   | Mengubah ambang batas (threshold) sensor |
| **DELETE** | Menghapus data     | Menghapus log data lama                  |

---

### 2. Format Data JSON

JSON (*JavaScript Object Notation*) adalah format pertukaran data yang ringan dan mudah dibaca baik oleh manusia maupun mesin.

```json
{
  "sensor": "DHT11",
  "suhu": 28.5,
  "kelembapan": 60,
  "status": "aman"
}
```

---

### 3. ESP32 sebagai HTTP Client

Untuk menggunakan HTTP pada ESP32, kita menggunakan library `HTTPClient.h`.

#### A. Mengirim Data (HTTP POST)

```cpp
#include <WiFi.h>
#include <HTTPClient.h>

const char* ssid = "NamaWiFi";
const char* password = "PasswordWiFi";
const char* serverName = "http://api.example.com/update-sensor";

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) { delay(500); }
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    http.begin(serverName);
    http.addHeader("Content-Type", "application/x-www-form-urlencoded");

    String httpRequestData = "api_key=tPmAT5Ab3j7F&value1=25.5";
    int httpResponseCode = http.POST(httpRequestData);

    if (httpResponseCode > 0) {
      Serial.print("HTTP Response code: ");
      Serial.println(httpResponseCode);
    }
    http.end();
  }
  delay(10000);
}
```

---

### 4. Implementasi Smarthome

Proyek Smarthome dengan REST API biasanya melibatkan:
1. **ESP32:** Sebagai pengumpul data sensor dan pelaksana perintah.
2. **Web Server (PHP/Node.js):** Sebagai perantara data.
3. **Database (MySQL/MongoDB):** Penyimpanan data historis.
4. **Dashboard Web:** Visualisasi data untuk pengguna.

---

## 🎥 Video Penjelasan

### Dasar Komunikasi Data HTTP

<iframe width="725" height="453" src="https://www.youtube.com/embed/5j466VQMZO4" frameborder="0" allowfullscreen></iframe>

### Playlist Proyek Smarthome REST API (15 Video)

[Tonton Playlist YouTube Lengkap](https://www.youtube.com/playlist?list=PLaC2GD6EmthX-Hn8zLGBSuZh-3k-yql-L)

---

## 📚 Sumber Belajar

- 📁 [Proyek GitHub - Smarthome dengan REST API](https://github.com/Muhammad-Ikhwan-Fathulloh/Smarthome-Rest-API)
- 📄 [Random Nerd Tutorials - ESP32 HTTP GET/POST](https://randomnerdtutorials.com/esp32-http-get-post-arduino/)
