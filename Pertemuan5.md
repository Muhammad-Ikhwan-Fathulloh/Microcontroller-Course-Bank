# Pertemuan 5: 📶 Mikrokontroler IoT: ESP32

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami arsitektur dan fitur board ESP32
2. Membandingkan ESP32 dengan Arduino Uno
3. Menginstal dan mengkonfigurasi ESP32 di Arduino IDE
4. Memanfaatkan fitur Wi-Fi dan Bluetooth bawaan ESP32
5. Membuat proyek IoT sederhana dengan ESP32

---

## 📚 Materi Pokok

### 1. Pengenalan ESP32

#### A. Apa itu ESP32?

**Definisi:** ESP32 adalah mikrokontroler System-on-Chip (SoC) buatan Espressif Systems yang memiliki fitur **Wi-Fi** dan **Bluetooth** terintegrasi, menjadikannya pilihan utama untuk proyek Internet of Things (IoT).

**Keunggulan:**
- Wi-Fi 802.11 b/g/n terintegrasi
- Bluetooth Classic & BLE (Bluetooth Low Energy)
- Dual-core processor (hingga 240 MHz)
- Harga terjangkau
- Kompatibel dengan Arduino IDE

#### B. Spesifikasi ESP32

| Spesifikasi      | Nilai                       |
| ---------------- | --------------------------- |
| Prosesor         | Xtensa Dual-Core 32-bit LX6 |
| Clock Speed      | Hingga 240 MHz              |
| Flash Memory     | 4 MB (hingga 16 MB)         |
| SRAM             | 520 KB                      |
| Wi-Fi            | 802.11 b/g/n (2.4 GHz)      |
| Bluetooth        | v4.2 BR/EDR & BLE           |
| GPIO             | 34 pin                      |
| ADC              | 18 channel (12-bit)         |
| DAC              | 2 channel (8-bit)           |
| PWM              | 16 channel                  |
| Tegangan Operasi | 3.3V                        |
| Tegangan Input   | 5V (via USB/VIN)            |

#### C. Perbandingan ESP32 vs Arduino Uno

| Fitur        | Arduino Uno        | ESP32                          |
| ------------ | ------------------ | ------------------------------ |
| Prosesor     | ATmega328P (8-bit) | Xtensa LX6 (32-bit, Dual-Core) |
| Clock        | 16 MHz             | 240 MHz                        |
| Flash        | 32 KB              | 4 MB                           |
| SRAM         | 2 KB               | 520 KB                         |
| Wi-Fi        | ❌ Tidak ada        | ✅ Built-in                     |
| Bluetooth    | ❌ Tidak ada        | ✅ Built-in                     |
| ADC          | 10-bit (6 channel) | 12-bit (18 channel)            |
| DAC          | ❌ Tidak ada        | ✅ 2 channel                    |
| Tegangan I/O | 5V                 | 3.3V                           |
| Harga        | ~Rp 50.000         | ~Rp 45.000                     |

> **⚠️ Penting:** Pin I/O ESP32 beroperasi pada **3.3V**. Jangan hubungkan langsung ke 5V karena bisa merusak board!

---

### 2. Pin Layout ESP32

#### A. Jenis-jenis Pin

| Jenis Pin | Jumlah | Keterangan                                  |
| --------- | ------ | ------------------------------------------- |
| GPIO      | 34     | General Purpose Input/Output                |
| ADC       | 18     | Analog to Digital (12-bit)                  |
| DAC       | 2      | Digital to Analog (GPIO 25, 26)             |
| Touch     | 10     | Capacitive Touch Sensor                     |
| SPI       | 4      | Serial Peripheral Interface                 |
| I2C       | 2      | Inter-Integrated Circuit                    |
| UART      | 3      | Universal Asynchronous Receiver/Transmitter |
| PWM       | 16     | Pulse Width Modulation                      |

#### B. Pin yang Perlu Diperhatikan

```
Pin khusus ESP32:
- GPIO 0   : Boot mode (jangan gunakan untuk input)
- GPIO 2   : LED onboard (beberapa board)
- GPIO 6-11: Flash SPI (JANGAN gunakan!)
- GPIO 34-39: Input only (tidak bisa output)
- GPIO 25  : DAC1
- GPIO 26  : DAC2
```

---

### 3. Instalasi ESP32 di Arduino IDE

#### A. Langkah Instalasi

1. Buka **Arduino IDE**
2. Klik **File → Preferences**
3. Di kolom **Additional Board Manager URLs**, tambahkan:

```
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
```

4. Klik **OK**
5. Buka **Tools → Board → Board Manager**
6. Cari **"esp32"** oleh Espressif Systems
7. Klik **Install**
8. Pilih board: **Tools → Board → ESP32 Dev Module**
9. Pilih port COM yang sesuai

#### B. Program Pertama: Blink LED

```cpp
const int ledPin = 2;  // LED onboard ESP32 (biasanya GPIO 2)

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(115200);  // ESP32 lebih baik pakai 115200
  Serial.println("ESP32 Ready!");
}

void loop() {
  digitalWrite(ledPin, HIGH);
  Serial.println("LED ON");
  delay(1000);

  digitalWrite(ledPin, LOW);
  Serial.println("LED OFF");
  delay(1000);
}
```

---

### 4. Wi-Fi pada ESP32

#### A. Mode Wi-Fi

| Mode                  | Fungsi                               | Kode                     |
| --------------------- | ------------------------------------ | ------------------------ |
| **Station (STA)**     | Terhubung ke router Wi-Fi            | `WiFi.mode(WIFI_STA)`    |
| **Access Point (AP)** | ESP32 membuat jaringan Wi-Fi sendiri | `WiFi.mode(WIFI_AP)`     |
| **STA + AP**          | Keduanya bersamaan                   | `WiFi.mode(WIFI_AP_STA)` |

#### B. Koneksi Wi-Fi (Mode Station)

```cpp
#include <WiFi.h>

const char* ssid = "Nama_WiFi_Anda";
const char* password = "Password_WiFi";

void setup() {
  Serial.begin(115200);

  // Koneksi ke Wi-Fi
  WiFi.begin(ssid, password);
  Serial.print("Menghubungkan ke WiFi");

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.println("Terhubung ke WiFi!");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  // Program utama
}
```

#### C. Web Server Sederhana

```cpp
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "Nama_WiFi";
const char* password = "Password";

WebServer server(80);

const int ledPin = 2;
bool ledState = false;

void handleRoot() {
  String html = "<html><body>";
  html += "<h1>ESP32 Web Server</h1>";
  html += "<p>LED Status: " + String(ledState ? "ON" : "OFF") + "</p>";
  html += "<a href='/on'><button>LED ON</button></a> ";
  html += "<a href='/off'><button>LED OFF</button></a>";
  html += "</body></html>";
  server.send(200, "text/html", html);
}

void handleOn() {
  ledState = true;
  digitalWrite(ledPin, HIGH);
  server.sendHeader("Location", "/");
  server.send(303);
}

void handleOff() {
  ledState = false;
  digitalWrite(ledPin, LOW);
  server.sendHeader("Location", "/");
  server.send(303);
}

void setup() {
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT);

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi terhubung! IP: " + WiFi.localIP().toString());

  server.on("/", handleRoot);
  server.on("/on", handleOn);
  server.on("/off", handleOff);
  server.begin();
}

void loop() {
  server.handleClient();
}
```

---

### 5. Bluetooth pada ESP32

#### A. Bluetooth Classic (Serial)

```cpp
#include "BluetoothSerial.h"

BluetoothSerial SerialBT;

const int ledPin = 2;

void setup() {
  Serial.begin(115200);
  SerialBT.begin("ESP32_BT");  // Nama perangkat Bluetooth
  pinMode(ledPin, OUTPUT);
  Serial.println("Bluetooth siap! Sambungkan melalui smartphone.");
}

void loop() {
  if (SerialBT.available()) {
    char perintah = SerialBT.read();

    if (perintah == '1') {
      digitalWrite(ledPin, HIGH);
      SerialBT.println("LED ON");
    } else if (perintah == '0') {
      digitalWrite(ledPin, LOW);
      SerialBT.println("LED OFF");
    }
  }
}
```

---

### 6. Fitur Tambahan ESP32

#### A. Touch Sensor (Capacitive Touch)

```cpp
const int touchPin = 4;  // GPIO 4 (Touch0)
const int ledPin = 2;
int threshold = 20;

void setup() {
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  int touchValue = touchRead(touchPin);
  Serial.println(touchValue);

  if (touchValue < threshold) {
    digitalWrite(ledPin, HIGH);
  } else {
    digitalWrite(ledPin, LOW);
  }
  delay(100);
}
```

#### B. DAC Output (Sinyal Analog Asli)

```cpp
const int dacPin = 25;  // DAC1 = GPIO 25

void setup() {
  Serial.begin(115200);
}

void loop() {
  // Buat gelombang naik turun
  for (int i = 0; i <= 255; i++) {
    dacWrite(dacPin, i);
    delay(5);
  }
  for (int i = 255; i >= 0; i--) {
    dacWrite(dacPin, i);
    delay(5);
  }
}
```

#### C. Deep Sleep (Hemat Daya)

```cpp
#define uS_TO_S_FACTOR 1000000
#define TIME_TO_SLEEP 5  // Tidur 5 detik

void setup() {
  Serial.begin(115200);
  Serial.println("ESP32 bangun! Melakukan tugas...");

  // Lakukan tugas (baca sensor, kirim data, dll)
  delay(2000);

  // Set waktu bangun
  esp_sleep_enable_timer_wakeup(TIME_TO_SLEEP * uS_TO_S_FACTOR);
  Serial.println("Tidur selama " + String(TIME_TO_SLEEP) + " detik...");

  // Masuk mode deep sleep
  esp_deep_sleep_start();
}

void loop() {
  // Tidak akan pernah dijalankan setelah deep sleep
}
```

---

## 🎥 Video Penjelasan

### Getting Started dengan ESP32 untuk IoT

<iframe width="725" height="453" src="https://www.youtube.com/embed/UYyfUeZkfBw" frameborder="0" allowfullscreen></iframe>

---

## 📚 Sumber Belajar

- 🌐 [Dokumentasi Resmi ESP32 - Espressif](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/)
- 📄 [Arduino ESP32 GitHub](https://github.com/espressif/arduino-esp32)
- 📚 [Install ESP32](https://randomnerdtutorials.com/installing-the-esp32-board-in-arduino-ide-windows-instructions/)
