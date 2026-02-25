# Pertemuan 4: 📊 ADC, DAC, dan PWM

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami konsep konversi sinyal analog ke digital (ADC)
2. Memahami konsep konversi sinyal digital ke analog (DAC)
3. Menerapkan teknik Pulse Width Modulation (PWM) untuk mengendalikan perangkat analog
4. Menggunakan potensiometer untuk mengatur kecerahan LED

---

## 📚 Materi Pokok

### 1. Sinyal Analog vs Digital

#### A. Sinyal Analog

**Definisi:** Sinyal yang memiliki nilai kontinu (tidak terbatas) dalam rentang tertentu.

**Contoh:** Suhu, cahaya, suara, tegangan dari sensor.

**Karakteristik:**
- Nilai berubah secara mulus dan kontinu
- Memiliki resolusi tak terbatas secara teoretis
- Rentan terhadap noise

#### B. Sinyal Digital

**Definisi:** Sinyal yang hanya memiliki dua keadaan: HIGH (1) atau LOW (0).

**Contoh:** Tombol ON/OFF, LED nyala/mati.

**Karakteristik:**
- Nilai diskrit (0 atau 1)
- Tahan terhadap noise
- Mudah diproses oleh mikrokontroler

---

### 2. ADC (Analog to Digital Converter)

#### A. Konsep Dasar

**Definisi:** ADC adalah proses mengubah sinyal analog (kontinu) menjadi nilai digital (diskrit) yang dapat diproses oleh mikrokontroler.

**Resolusi ADC pada Arduino Uno:**
- 10-bit → 2¹⁰ = **1024 level** (0 – 1023)
- Tegangan referensi: 5V
- Resolusi per step: 5V / 1024 = **4.88 mV**

#### B. Rumus Konversi

```
Nilai Digital = (Tegangan Input / Tegangan Referensi) × 1023

Tegangan Input = (Nilai Digital / 1023) × Tegangan Referensi
```

**Contoh:**
- Input 2.5V → Nilai Digital = (2.5 / 5) × 1023 = **511**
- Nilai Digital 768 → Tegangan = (768 / 1023) × 5 = **3.75V**

#### C. Program Baca ADC

```cpp
const int sensorPin = A0;  // Pin analog A0

void setup() {
  Serial.begin(9600);
}

void loop() {
  int nilaiADC = analogRead(sensorPin);  // Baca ADC (0-1023)

  // Konversi ke tegangan
  float tegangan = nilaiADC * (5.0 / 1023.0);

  Serial.print("Nilai ADC: ");
  Serial.print(nilaiADC);
  Serial.print(" | Tegangan: ");
  Serial.print(tegangan);
  Serial.println(" V");

  delay(500);
}
```

#### D. Pin Analog Arduino Uno

| Pin | Fungsi                     |
| --- | -------------------------- |
| A0  | Analog Input 0             |
| A1  | Analog Input 1             |
| A2  | Analog Input 2             |
| A3  | Analog Input 3             |
| A4  | Analog Input 4 / SDA (I2C) |
| A5  | Analog Input 5 / SCL (I2C) |

---

### 3. DAC (Digital to Analog Converter)

#### A. Konsep Dasar

**Definisi:** DAC adalah proses mengubah nilai digital menjadi sinyal analog. Arduino Uno **tidak memiliki DAC hardware**, tetapi menggunakan **PWM** sebagai pengganti.

**Catatan:** ESP32 memiliki DAC hardware bawaan pada pin DAC1 (GPIO 25) dan DAC2 (GPIO 26).

---

### 4. PWM (Pulse Width Modulation)

#### A. Konsep Dasar

**Definisi:** PWM adalah teknik modulasi yang mengubah lebar pulsa sinyal digital untuk mensimulasikan sinyal analog.

**Istilah Penting:**
- **Duty Cycle:** Persentase waktu sinyal HIGH dalam satu periode
- **Periode:** Waktu untuk satu siklus penuh
- **Frekuensi:** Jumlah siklus per detik

#### B. Duty Cycle

| Duty Cycle | Nilai analogWrite() | Tegangan Efektif |
| ---------- | ------------------- | ---------------- |
| 0%         | 0                   | 0V               |
| 25%        | 64                  | 1.25V            |
| 50%        | 127                 | 2.5V             |
| 75%        | 191                 | 3.75V            |
| 100%       | 255                 | 5V               |

#### C. Pin PWM Arduino Uno

Pin yang mendukung PWM ditandai dengan tanda **~** pada board:

| Pin | Frekuensi PWM |
| --- | ------------- |
| 3   | ~490 Hz       |
| 5   | ~980 Hz       |
| 6   | ~980 Hz       |
| 9   | ~490 Hz       |
| 10  | ~490 Hz       |
| 11  | ~490 Hz       |

#### D. Program LED Dimmer dengan PWM

```cpp
const int ledPin = 9;  // Pin PWM

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  // Fade in (terang perlahan)
  for (int brightness = 0; brightness <= 255; brightness++) {
    analogWrite(ledPin, brightness);
    delay(10);
  }

  // Fade out (redup perlahan)
  for (int brightness = 255; brightness >= 0; brightness--) {
    analogWrite(ledPin, brightness);
    delay(10);
  }
}
```

---

### 5. Aplikasi: ADC + PWM (Potensiometer → LED)

**Deskripsi:** Potensiometer mengatur kecerahan LED menggunakan ADC dan PWM.

```cpp
const int potPin = A0;   // Potensiometer di A0
const int ledPin = 9;    // LED di pin PWM 9

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  int nilaiADC = analogRead(potPin);       // Baca ADC (0-1023)
  int nilaiPWM = map(nilaiADC, 0, 1023, 0, 255);  // Konversi ke PWM (0-255)

  analogWrite(ledPin, nilaiPWM);  // Atur kecerahan LED

  Serial.print("ADC: ");
  Serial.print(nilaiADC);
  Serial.print(" | PWM: ");
  Serial.println(nilaiPWM);

  delay(100);
}
```

> **💡 Fungsi `map()`:** Mengkonversi rentang nilai dari satu skala ke skala lainnya.
> Sintaks: `map(value, fromLow, fromHigh, toLow, toHigh)`

---

### 6. Aplikasi: LDR (Light Dependent Resistor)

**Deskripsi:** Sensor cahaya yang mengubah resistansi berdasarkan intensitas cahaya.

```cpp
const int ldrPin = A0;    // LDR di A0
const int ledPin = 9;     // LED di pin PWM

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  int nilaiCahaya = analogRead(ldrPin);

  // Semakin gelap → LED semakin terang (inversi)
  int brightness = map(nilaiCahaya, 0, 1023, 255, 0);
  analogWrite(ledPin, brightness);

  Serial.print("Cahaya: ");
  Serial.print(nilaiCahaya);
  Serial.print(" | LED: ");
  Serial.println(brightness);

  delay(200);
}
```

---

## 🎮 Simulasi Interaktif Tinkercad

### 📊 LED dengan ADC dan PWM

Simulasi pengaturan kecerahan LED menggunakan potensiometer (ADC) dan PWM:

<iframe width="725" height="453" src="https://www.tinkercad.com/embed/1bygqGovE3W?editbtn=1" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

**Cara Menggunakan Simulasi:**

1. **Jalankan Simulasi:** Klik tombol "Start Simulation"
2. **Putar Potensiometer:** Klik dan geser potensiometer untuk mengubah nilai
3. **Amati LED:** Perhatikan perubahan kecerahan LED
4. **Buka Serial Monitor:** Lihat nilai ADC dan PWM secara real-time
5. **Eksperimen:** Ganti LED dengan motor DC untuk mengatur kecepatan

---

## 🎥 Video Penjelasan

### Konsep ADC dan PWM pada Arduino

<iframe width="725" height="453" src="https://www.youtube.com/embed/w54Nw1wsCKo" frameborder="0" allowfullscreen></iframe>

---

## 📚 Sumber Belajar

- 🔗 [Simulasi Tinkercad - LED dengan ADC dan PWM](https://www.tinkercad.com/things/3ootQIS5lMZ-led-adc-dan-pwm)
