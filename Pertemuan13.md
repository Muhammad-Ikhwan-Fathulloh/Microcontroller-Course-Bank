# Pertemuan 13: 🤖 Robotika: Mobile Robot

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Mengenal konsep robot mobile dengan roda omnidireksional (*Omniwheel*)
2. Memahami arsitektur kontrol robot menggunakan protokol MQTT
3. Merancang sistem navigasi sederhana (maju, mundur, geser, rotasi)
4. Mengintegrasikan ESP32 dengan driver motor L298N untuk penggerak robot

---

## 📚 Materi Pokok

### 1. Pengenalan KoKuBot (Omniwheel Robot)

#### A. Apa itu Omniwheel?
Roda omni (*Omniwheel*) memiliki roda-roda kecil di sekeliling diameternya yang tegak lurus dengan arah putaran roda utama. Hal ini memungkinkan robot bergerak ke segala arah (maju, mundur, samping, diagonal) tanpa harus memutar badan robot terlebih dahulu.

#### B. Konfigurasi 4-Omniwheel
Dengan 4 roda yang dipasang bersilangan, robot dapat melakukan pergerakan:
- **Lateral:** Geser ke kanan atau ke kiri.
- **Diagonal:** Gabungan gerakan linear.
- **Rotation:** Berputar di tempat (pivot).

---

### 2. Arsitektur Kontrol via MQTT

Mobile robot ini dikendalikan secara nirkabel melalui broker MQTT.

- **Broker:** Sebagai perantara antara aplikasi pengendali (Dashboard) dan Robot.
- **Topic `robot/control`:** Digunakan untuk mengirim perintah (`forward`, `backward`, `left`, `right`, `rotate_left`, `stop`).
- **Topic `robot/status`:** Digunakan robot untuk melaporkan status (misal: tegangan baterai atau jarak halangan).

---

### 3. Koneksi Hardware

| Komponen             | Fungsi                                             |
| -------------------- | -------------------------------------------------- |
| **ESP32**            | Otak pemroses perintah dan koneksi WiFi/MQTT       |
| **2x L298N**         | Driver motor (masing-masing mengontrol 2 motor DC) |
| **4x Motor DC**      | Penggerak roda omni                                |
| **Battery 7.4V-12V** | Sumber daya motor dan mikrokontroler               |

---

### 4. Contoh Logika Kontrol (Pseudocode)

```cpp
void moveRobot(String command) {
  if (command == "forward") {
    // Gerakkan semua motor ke depan
  } else if (command == "right") {
    // Kombinasi arah motor untuk geser kanan
  } else if (command == "turn_left") {
    // Putar motor kiri mundur, motor kanan maju
  } else if (command == "stop") {
    // Matikan semua PWM motor
  }
}
```

---

## 📚 Repositori & Proyek

### KoKuBot (Mobile Robot)
Berisi kode sumber untuk berbagai fitur, diagram koneksi hardware, dan dokumentasi lengkap.

- 📁 [Repositori GitHub](https://github.com/Muhammad-Ikhwan-Fathulloh/KoKuBot)
- 🌐 [Halaman Proyek KoKuBot](https://ikhwanfathulloh.netlify.app/kokubot)

---

## 🎥 Video Penjelasan

*Cari demo robot di saluran YouTube utama atau repositori terkait untuk melihat implementasi nyatanya.*

---

## 📚 Sumber Belajar

- 📄 [Omnidirectional Mobile Robot Theory](https://modernrobotics.northwestern.edu/nu-gm-book-resource/13-2-omnidirectional-wheeled-mobile-robots/)
- 📄 [MQTT Explorer for Debugging](http://mqtt-explorer.com/)
