# Pertemuan 11: 🧠 Artificial Intelligence (AI) pada ESP32

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami konsep Edge AI dan TinyML (Tiny Machine Learning)
2. Mengenal alur kerja (workflow) pembuatan model AI untuk mikrokontroler
3. Menggunakan library TensorFlow Lite for Microcontrollers di ESP32
4. Mengimplementasikan model prediksi sederhana (regresi/klasifikasi) pada hardware

---

### 🔄 TinyML Workflow

```mermaid
graph LR
    A[Data Collection] --> B[Model Training - PC/Cloud]
    B --> C[Model Conversion - TFLite]
    C --> D[Model.h - C++ Header]
    D --> E[Deployment - ESP32]
    E --> F[Inference - Real-time Prediction]
```

---

## 📚 Materi Pokok

### 1. Apa itu Edge AI?

#### A. Definisi
Edge AI adalah pemrosesan kecerdasan buatan (AI) yang dilakukan langsung pada perangkat fisik (edge device) seperti mikrokontroler, tanpa harus mengirim data ke cloud.

#### B. Keunggulan TinyML
- **Latency Rendah:** Keputusan diambil instan tanpa menunggu koneksi internet.
- **Privasi:** Data tidak keluar dari perangkat.
- **Bandwidth Hemat:** Tidak perlu mengirim data sensor mentah ke server.
- **Konsumsi Daya Rendah:** Perangkat bisa berjalan dengan baterai.

---

### 2. Workflow AI pada Mikrokontroler

Proses pembuatan AI untuk ESP32 berbeda dengan AI biasa karena keterbatasan memori (RAM & Flash).

1. **Data Collection:** Mengambil sampel data dari sensor.
2. **Model Training:** Melatih model menggunakan framework seperti TensorFlow/Keras di PC/Colab.
3. **Model Conversion:** Mengubah model `.h5` atau `.tflite` menjadi format array C++ (`.h`).
4. **Deployment:** Memasukkan model ke dalam kode program ESP32.
5. **Inference:** Menjalankan model pada ESP32 untuk mendapatkan prediksi.

---

### 3. TensorFlow Lite for Microcontrollers

TensorFlow Lite (TFLite) adalah library yang dioptimalkan untuk menjalankan model machine learning pada perangkat seluler dan embedded.

#### A. Persiapan Library
1. Cari library **"EloquentTinyML"** atau **"TensorFlowLite_ESP32"** di Library Manager Arduino IDE.
2. Siapkan data model dalam format header file (`model_data.h`).

#### B. Contoh Implementasi: Prediksi BMI

```cpp
#include <EloquentTinyML.h>
#include "model_data.h" // File header berisi model yang sudah dilatih

#define NUMBER_OF_INPUTS 2
#define NUMBER_OF_OUTPUTS 1
#define TENSOR_ARENA_SIZE 2*1024

Eloquent::TinyML::TfLite<NUMBER_OF_INPUTS, NUMBER_OF_OUTPUTS, TENSOR_ARENA_SIZE> ml;

void setup() {
  Serial.begin(115200);
  ml.begin(model_data); // Memuat model
}

void loop() {
  float input[2] = {170.0, 70.0}; // Tinggi (cm) dan Berat (kg)
  float predicted_bmi = ml.predict(input);
  
  Serial.print("Hasil Prediksi BMI: ");
  Serial.println(predicted_bmi);
  delay(5000);
}
```

---

### 4. Tantangan TinyML

- **Memori Terbatas:** Harus menggunakan model yang sangat kecil (biasanya < 100KB).
- **Floating Point:** ESP32 mendukung floating point, namun optimisasi "Quantization" (mengubah float ke integer) sangat disarankan untuk kecepatan.

---

## 🎥 Video Penjelasan

### Tutorial AI untuk ESP32

<iframe width="725" height="453" src="https://www.youtube.com/embed/videoseries?list=PLaC2GD6EmthVkj6hL_Aq0lwUAtMNX4Nla" frameborder="0" allowfullscreen></iframe>

---

## 📚 Sumber Belajar

- 📁 [Proyek GitHub - Prediksi BMI dengan ESP32](https://github.com/Muhammad-Ikhwan-Fathulloh/BMI-Prediction-with-ESP32)
- 🌐 [TensorFlow Lite for Microcontrollers Documentation](https://www.tensorflow.org/lite/microcontrollers)
- 📄 [Edge Impulse - Platform TinyML No-Code](https://www.edgeimpulse.com/)
