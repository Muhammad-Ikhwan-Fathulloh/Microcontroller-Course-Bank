# Pertemuan 12: 🤖 Robotika: Arm Robotik

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Mengenal jenis-jenis motor servo untuk aplikasi robotika
2. Memahami konsep Derajat Kebebasan (*Degree of Freedom* / DoF) pada robot
3. Mengontrol pergerakan lengan robot melalui mikrokontroler
4. Menjalankan urutan pergerakan (*motion sequence*) secara otomatis

---

## 📚 Materi Pokok

### 1. Dasar Robot Arm

#### A. Apa itu Arm Robotik?
Arm robotik adalah jenis lengan mekanis yang biasanya dapat diprogram, dengan fungsi yang mirip dengan lengan manusia. Lengan ini dapat menjadi mekanisme utuh atau bisa menjadi bagian dari robot yang lebih kompleks.

#### B. Derajat Kebebasan (DoF)
*Degree of Freedom* (DoF) menentukan fleksibilitas gerakan robot.
- **3-DoF:** Biasanya memiliki sendi bahu, siku, dan pergelangan dasar.
- **6-DoF:** Standar industri yang memungkinkan robot mencapai posisi dan orientasi apapun di ruang kerja.

---

### 2. Komponen Utama

1. **Mikrokontroler:** Arduino atau ESP32 sebagai pemberi sinyal PWM.
2. **Servo Motor:** Aktuator utama (Contoh: MG90S, MG996R).
3. **Power Supply:** Diperlukan daya eksternal (5V-6V) karena servo mengonsumsi arus yang besar saat bergerak.
4. **Struktur Mekanik:** Rangka lengan yang terbuat dari akrilik, aluminium, atau hasil 3D print.

---

### 3. Kontrol Multi-Servo

Untuk mengontrol banyak servo sekaligus, kita bisa menggunakan library standar `Servo.h` (Arduino) atau `ESP32Servo.h` (ESP32).

#### Contoh Kode: Gerakan Dasar Lengan Robot

```cpp
#include <Servo.h>

Servo baseServo;
Servo elbowServo;
Servo gripperServo;

void setup() {
  baseServo.attach(9);     // Pin Pin 9
  elbowServo.attach(10);    // Pin 10
  gripperServo.attach(11);  // Pin 11
  
  // Posisi Home
  baseServo.write(90);
  elbowServo.write(90);
  gripperServo.write(0);
}

void loop() {
  // Ambil Barang
  moveSlowly(baseServo, 90, 45); // Gerak ke kiri
  moveSlowly(elbowServo, 90, 130); // Turun
  moveSlowly(gripperServo, 0, 90); // Jepit
  delay(1000);
  
  // Angkat Barang
  moveSlowly(elbowServo, 130, 90); // Naik
  moveSlowly(baseServo, 45, 135); // Putar ke kanan
  delay(1000);
}

// Fungsi agar gerakan tidak terlalu menghentak
void moveSlowly(Servo &s, int from, int to) {
  if (from < to) {
    for (int i = from; i <= to; i++) {
      s.write(i);
      delay(15);
    }
  } else {
    for (int i = from; i >= to; i--) {
      s.write(i);
      delay(15);
    }
  }
}
```

---

### 4. Konsep Kinematika (Pengenalan)

1. **Forward Kinematics:** Menghitung posisi ujung lengan (*End Effector*) berdasarkan sudut-sudut sendi.
2. **Inverse Kinematics:** Menghitung berapa sudut masing-masing sendi yang diperlukan agar ujung lengan mencapai koordinat (X, Y, Z) tertentu.

---

## 📚 Sumber Belajar

- 📁 [Repositori GitHub - Arm Robotik](https://github.com/Muhammad-Ikhwan-Fathulloh/Arm-Robotik)
- 📄 [How to build a Simple Arduino Robot Arm](https://howtomechatronics.com/tutorials/arduino/diy-arduino-robot-arm-project/)
- 🎥 [Tutorial Robot Arm - YouTube](https://www.youtube.com/results?search_query=arduino+robot+arm+tutorial)
