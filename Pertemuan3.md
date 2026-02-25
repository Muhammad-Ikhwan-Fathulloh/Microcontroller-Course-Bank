# Pertemuan 3: 📡 Komunikasi Data Serial, Sensor, dan Aktuator

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami konsep komunikasi data serial (UART)
2. Menggunakan Serial Monitor untuk debugging dan monitoring
3. Menghubungkan dan membaca data dari sensor (input)
4. Mengendalikan aktuator (output) berdasarkan data sensor

---

## 📚 Materi Pokok

### 1. Komunikasi Serial (UART)

#### A. Konsep Dasar

**Definisi:** Komunikasi serial adalah metode pengiriman data secara berurutan (bit per bit) melalui satu jalur komunikasi.

**Protokol UART (Universal Asynchronous Receiver/Transmitter):**
- Tidak memerlukan sinyal clock bersama (asynchronous)
- Menggunakan dua jalur: **TX** (Transmit) dan **RX** (Receive)
- Baud rate harus sama di kedua perangkat

#### B. Konfigurasi Serial pada Arduino

```cpp
void setup() {
  Serial.begin(9600);  // Memulai komunikasi serial dengan baud rate 9600
}

void loop() {
  Serial.println("Hello, Serial!");  // Mengirim data ke Serial Monitor
  delay(1000);
}
```

#### C. Baud Rate Umum

| Baud Rate | Kegunaan                                 |
| --------- | ---------------------------------------- |
| 9600      | Default, cocok untuk debugging           |
| 115200    | Kecepatan tinggi, cocok untuk data besar |
| 4800      | Kecepatan rendah, jarak jauh             |

#### D. Fungsi Serial Penting

```cpp
Serial.begin(baudrate);      // Memulai komunikasi serial
Serial.print(data);          // Mengirim data tanpa newline
Serial.println(data);        // Mengirim data dengan newline
Serial.read();               // Membaca 1 byte dari buffer
Serial.available();          // Cek jumlah byte yang tersedia
Serial.parseInt();           // Membaca integer dari serial
Serial.parseFloat();         // Membaca float dari serial
```

> **💡 Tips:** `Serial.available()` berguna untuk mengecek apakah ada data yang masuk sebelum membacanya, menghindari error saat buffer kosong.

---

### 2. Sensor (Input)

#### A. Sensor Ultrasonik (HC-SR04)

**Fungsi:** Mengukur jarak menggunakan gelombang ultrasonik.

**Spesifikasi:**

| Parameter          | Nilai         |
| ------------------ | ------------- |
| Tegangan Operasi   | 5V DC         |
| Rentang Pengukuran | 2 cm – 400 cm |
| Akurasi            | ±3 mm         |
| Sudut Pengukuran   | 15°           |

**Pin:**
- **VCC** → 5V
- **Trig** → Pin Digital (Output)
- **Echo** → Pin Digital (Input)
- **GND** → GND

**Prinsip Kerja:**

```
1. Trig mengirim sinyal ultrasonik (pulsa HIGH 10µs)
2. Gelombang memantul dari objek
3. Echo menerima pantulan
4. Jarak = (waktu × kecepatan suara) / 2
```

**Program:**

```cpp
const int trigPin = 9;
const int echoPin = 10;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

void loop() {
  // Kirim pulsa trigger
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Baca durasi echo
  long durasi = pulseIn(echoPin, HIGH);

  // Hitung jarak (cm)
  float jarak = durasi * 0.034 / 2;

  Serial.print("Jarak: ");
  Serial.print(jarak);
  Serial.println(" cm");

  delay(500);
}
```

#### B. Sensor Suhu & Kelembapan (DHT11)

**Fungsi:** Mengukur suhu dan kelembapan udara.

**Spesifikasi:**

| Parameter          | Nilai             |
| ------------------ | ----------------- |
| Tegangan Operasi   | 3.3V – 5V         |
| Rentang Suhu       | 0°C – 50°C (±2°C) |
| Rentang Kelembapan | 20% – 80% (±5%)   |
| Waktu Sampling     | 1 detik           |

**Program:**

```cpp
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
}

void loop() {
  float suhu = dht.readTemperature();
  float kelembapan = dht.readHumidity();

  if (isnan(suhu) || isnan(kelembapan)) {
    Serial.println("Gagal membaca sensor DHT!");
    return;
  }

  Serial.print("Suhu: ");
  Serial.print(suhu);
  Serial.print(" °C | Kelembapan: ");
  Serial.print(kelembapan);
  Serial.println(" %");

  delay(2000);
}
```

---

### 3. Aktuator (Output)

#### A. Motor Servo

**Fungsi:** Motor yang dapat diatur sudut putarannya (0° – 180°).

**Koneksi:**
- **Merah** → 5V
- **Coklat/Hitam** → GND
- **Oranye/Kuning** → Pin PWM

**Program:**

```cpp
#include <Servo.h>

Servo myServo;

void setup() {
  myServo.attach(9);  // Servo di pin 9
  Serial.begin(9600);
}

void loop() {
  // Sweep dari 0 ke 180 derajat
  for (int sudut = 0; sudut <= 180; sudut++) {
    myServo.write(sudut);
    delay(15);
  }

  // Sweep dari 180 ke 0 derajat
  for (int sudut = 180; sudut >= 0; sudut--) {
    myServo.write(sudut);
    delay(15);
  }
}
```

#### B. LED sebagai Indikator

**Program Sensor + Aktuator (Ultrasonik + LED):**

```cpp
const int trigPin = 9;
const int echoPin = 10;
const int ledHijau = 3;
const int ledKuning = 4;
const int ledMerah = 5;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(ledHijau, OUTPUT);
  pinMode(ledKuning, OUTPUT);
  pinMode(ledMerah, OUTPUT);
}

void loop() {
  // Ukur jarak
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long durasi = pulseIn(echoPin, HIGH);
  float jarak = durasi * 0.034 / 2;

  // Nyalakan LED berdasarkan jarak
  digitalWrite(ledHijau, LOW);
  digitalWrite(ledKuning, LOW);
  digitalWrite(ledMerah, LOW);

  if (jarak > 30) {
    digitalWrite(ledHijau, HIGH);    // Aman
  } else if (jarak > 10) {
    digitalWrite(ledKuning, HIGH);   // Waspada
  } else {
    digitalWrite(ledMerah, HIGH);    // Bahaya
  }

  Serial.print("Jarak: ");
  Serial.print(jarak);
  Serial.println(" cm");

  delay(200);
}
```

---

### 4. Kontrol Serial (Input dari Keyboard)

**Program kontrol LED via Serial Monitor:**

```cpp
const int led = 13;

void setup() {
  Serial.begin(9600);
  pinMode(led, OUTPUT);
  Serial.println("Ketik '1' untuk ON, '0' untuk OFF");
}

void loop() {
  if (Serial.available() > 0) {
    char perintah = Serial.read();

    if (perintah == '1') {
      digitalWrite(led, HIGH);
      Serial.println("LED ON");
    } else if (perintah == '0') {
      digitalWrite(led, LOW);
      Serial.println("LED OFF");
    }
  }
}
```

---

## 🎮 Simulasi Interaktif Tinkercad

### 📡 Komunikasi Serial, Sensor, dan Aktuator

Simulasi lengkap yang menggabungkan komunikasi serial dengan sensor dan aktuator:

<iframe width="725" height="453" src="https://www.tinkercad.com/embed/ioII5OI8Wt2?editbtn=1" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

**Cara Menggunakan Simulasi:**

1. **Jalankan Simulasi:** Klik tombol "Start Simulation"
2. **Buka Serial Monitor:** Klik "Serial Monitor" di bawah simulasi
3. **Amati Data:** Lihat pembacaan sensor secara real-time
4. **Kirim Perintah:** Ketik perintah di Serial Monitor untuk mengontrol aktuator
5. **Eksperimen:** Ubah jarak objek di depan sensor ultrasonik

### 📡 Relay untuk mengontrol beban AC

<iframe width="725" height="453" src="https://www.tinkercad.com/embed/iaD2kdCQbbw?editbtn=1" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

---

## 📚 Sumber Belajar

- 🔗 [Simulasi Tinkercad - Komunikasi Serial](https://www.tinkercad.com/things/eJKRDjfadeZ-komunikasi-serial-sensor-dan-aktuator)

### 🎥 Playlist Serial Komunikasi untuk Sensor dan Aktuator

<iframe width="725" height="453" src="https://www.youtube.com/embed/videoseries?list=PLaC2GD6EmthWsrz53AchCrQu5HNMtjvYC" frameborder="0" allowfullscreen></iframe>
