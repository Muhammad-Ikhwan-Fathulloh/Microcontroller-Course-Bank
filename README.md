# 📚 Bank Materi Mata Kuliah Mikrokontroler

Selamat datang di repositori bank materi untuk mata kuliah Mikrokontroler. Daftar ini disusun secara sistematis untuk membantu Anda mempelajari konsep dasar hingga implementasi proyek Internet of Things (IoT) menggunakan platform Arduino dan ESP32.

---

## 1. 🔌 Dasar Elektronika: Arus, Tegangan, dan Daya
**Deskripsi:** Pengenalan konsep fundamental kelistrikan yang menjadi dasar dalam merancang dan memahami rangkaian elektronika.
- **Komponen Utama:** Resistor, Dioda, Power Supply.
- **Rangkaian Dioda Bridge:**
    - 🔗 [Simulasi Tinkercad - Dioda Bridge](https://www.tinkercad.com/things/2mhRJJv0s8d-dioda-bridge) *(Simulasikan cara kerja penyearah gelombang)*
    - 🎥 [Penjelasan YouTube - Cara Kerja Dioda Bridge](https://www.youtube.com/watch?v=n-bfkUUbT6k)

---

## 2. 💻 Pengenalan Mikrokontroler Arduino dan Pemrograman C++
**Deskripsi:** Memahami arsitektur Arduino dan dasar-dasar bahasa pemrograman C++ untuk mengendalikan komponen elektronik.
- **Komponen Utama:** Arduino Uno/Nano, LED, Resistor, Breadboard.
- **Materi Belajar:**
    - 📄 [Artikel Medium - Dasar Pemrograman Arduino C++](https://medium.com/@ikhwanfathulloh/pemograman-dasar-arduino-bbec7bb6ec11)
    - 🎥 [Playlist YouTube - Tutorial Pemrograman Arduino untuk Pemula](https://www.youtube.com/playlist?list=PLaC2GD6EmthV7dJBrq9eMw0NNCk83fLxs)

---

## 3. 📡 Komunikasi Data Serial, Sensor, dan Aktuator
**Deskripsi:** Implementasi komunikasi serial untuk menghubungkan mikrokontroler dengan berbagai sensor (input) dan aktuator (output).
- **Komponen Utama:** Sensor Ultrasonik / DHT11, Motor Servo / LED, Arduino.
- **Rangkaian & Penjelasan:**
    - 🔗 [Simulasi Tinkercad - Komunikasi Serial](https://www.tinkercad.com/things/eJKRDjfadeZ-komunikasi-serial-sensor-dan-aktuator)
    - 🎥 [Playlist YouTube - Serial Komunikasi untuk Sensor dan Aktuator](https://www.youtube.com/playlist?list=PLaC2GD6EmthWsrz53AchCrQu5HNMtjvYC)

---

## 4. 📊 ADC, DAC, dan PWM
**Deskripsi:** Mempelajari konversi sinyal analog ke digital (ADC), digital ke analog (DAC), serta teknik modulasi lebar pulsa (PWM) untuk mengendalikan perangkat analog.
- **Komponen Utama:** Potensiometer, LED, LDR.
- **Rangkaian & Penjelasan:**
    - 🔗 [Simulasi Tinkercad - LED dengan ADC dan PWM](https://www.tinkercad.com/things/3ootQIS5lMZ-led-adc-dan-pwm) *(Atur kecerahan LED dengan potensiometer)*
    - 🎥 [Penjelasan YouTube - Konsep ADC dan PWM pada Arduino](https://youtu.be/w54Nw1wsCKo?si=_TyPFZ4oJV4WIiaQ)

---

## 5. 📶 Mikrokontroler IoT: ESP32
**Deskripsi:** Pengenalan board ESP32 sebagai mikrokontroler dengan fitur Wi-Fi dan Bluetooth terintegrasi untuk proyek IoT.
- **Komponen Utama:** Board ESP32, LED, Sensor.
- **Materi Belajar:**
    - 🎥 [Penjelasan YouTube - Getting Started dengan ESP32 untuk IoT](https://youtu.be/UYyfUeZkfBw?si=C8z9gknJ-bqOPe-c)

---

## 6. ☁️ Platform Internet of Things: Blynk
**Deskripsi:** Membangun antarmuka kontrol dan monitoring untuk perangkat IoT menggunakan platform Blynk (aplikasi smartphone).
- **Komponen Utama:** ESP32, Smartphone (Aplikasi Blynk).
- **Materi Belajar:**
    - 🎥 [Penjelasan YouTube - Integrasi ESP32 dengan Blynk](https://youtu.be/pI9clelWYkI?si=BPgd_J8f4GfLnMrP)

---

## 7. 🌐 Protokol Komunikasi: HTTP (REST API)
**Deskripsi:** Mempelajari cara mengirim dan menerima data antara ESP32 dan server/web menggunakan protokol HTTP.
- **Komponen Utama:** ESP32, Server Web, Database.
- **Materi & Proyek:**
    - 🎥 [Penjelasan YouTube - Dasar Komunikasi Data HTTP](https://youtu.be/5j466VQMZO4?si=0sdtldkEQWhtTDg9)
    - 📁 [Proyek GitHub - Smarthome dengan REST API](https://github.com/Muhammad-Ikhwan-Fathulloh/Smarthome-Rest-API)

---

## 8. ↔️ Protokol Komunikasi: MQTT
**Deskripsi:** Implementasi protokol MQTT (Message Queuing Telemetry Transport) yang ringan untuk komunikasi *machine-to-machine* (M2M) yang efisien.
- **Komponen Utama:** ESP32, Broker MQTT (Mosquitto/HiveMQ).
- **Materi & Proyek:**
    - 🎥 [Penjelasan YouTube - Dasar Komunikasi Data MQTT](https://youtu.be/5j466VQMZO4?si=0sdtldkEQWhtTDg9) *(Video yang sama, perhatikan perbandingan protokol)*
    - 📁 [Proyek GitHub - Smarthome dengan MQTT](https://github.com/Muhammad-Ikhwan-Fathulloh/Smarthome-MQTT)

---

## 9. ⏱️ Real-Time Operating System (RTOS) pada ESP32
**Deskripsi:** Memahami konsep *multitasking* dengan FreeRTOS pada ESP32 untuk menjalankan beberapa tugas secara bersamaan dan efisien.
- **Komponen Utama:** ESP32, FreeRTOS Library.
- **Materi & Proyek:**
    - 🎥 [Playlist YouTube - Tutorial FreeRTOS pada ESP32](https://www.youtube.com/playlist?list=PLaC2GD6EmthVB7f7ZJjgGAU9K3ylxzDMJ)
    - 📁 [Proyek GitHub - Implementasi FreeRTOS pada ESP32](https://github.com/Muhammad-Ikhwan-Fathulloh/ESP32-dengan-Free-RTOS)

---

## 10. 🧠 Artificial Intelligence (AI) pada ESP32
**Deskripsi:** Penerapan kecerdasan buatan sederhana (seperti machine learning) pada perangkat edge menggunakan ESP32 untuk prediksi data.
- **Komponen Utama:** ESP32, TensorFlow Lite untuk Mikrokontroler.
- **Materi & Proyek:**
    - 🎥 [Playlist YouTube - Tutorial AI untuk ESP32](https://www.youtube.com/playlist?list=PLaC2GD6EmthVkj6hL_Aq0lwUAtMNX4Nla)
    - 📁 [Proyek GitHub - Prediksi BMI dengan ESP32](https://github.com/Muhammad-Ikhwan-Fathulloh/BMI-Prediction-with-ESP32)

---

## 📌 Sumber Utama
- 🎥 **Playlist Kuliah Lengkap:** [Kuliah Mikrokontroler - YouTube](https://www.youtube.com/playlist?list=PLaC2GD6EmthX4x-tkUo6ZEe1dqd6e6vex)
- 🔧 **Koleksi Simulasi:** [Profil Tinkercad - Muhammad Ikhwan Fathulloh](https://www.tinkercad.com/users/fLJ4Cr1QCXa?type=circuits)
