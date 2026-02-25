# Pertemuan 2: 💻 Pengenalan Arduino & Pemrograman C++

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

### 📌 Dasar Pemrograman
- Memahami arsitektur Arduino Uno/Nano
- Mengenal IDE Arduino
- Struktur program (setup & loop)
- Variabel dan tipe data

### 🚀 Implementasi
- Struktur kontrol (if, switch, for, while)
- Array dan fungsi
- Built-in functions Arduino
- Proyek LED Blink & Traffic Light

---

## 🎮 Simulasi Interaktif Tinkercad

### 🔘 LED dengan Button

Simulasi LED yang dikendalikan dengan tombol. Tekan tombol untuk menyalakan/mematikan LED.

<iframe width="725" height="453" src="https://www.tinkercad.com/embed/lf83RHYS4cT?editbtn=1" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

**Konsep:** Digital Read, If-Else, Pull-up Resistor

---

### 🏃 Running LED

Simulasi LED berjalan (running LED) dengan 8 LED yang menyala bergantian.

<iframe width="725" height="453" src="https://www.tinkercad.com/embed/3jERpIjfvOG?editbtn=1" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

**Konsep:** Array, For Loop, Delay

---

### ✨ Animasi LED

Simulasi animasi LED dengan berbagai pola menyala dan efek visual.

<iframe width="725" height="453" src="https://www.tinkercad.com/embed/kHLovVT7tI1?editbtn=1" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

**Konsep:** Array 2 Dimensi, Fungsi, Switch-Case

---

## 1. 🔧 Pengenalan Arduino

### A. Jenis-jenis Board Arduino

| Board    | Mikrokontroler | Tegangan | Flash  | Digital I/O | Analog |
| -------- | -------------- | -------- | ------ | ----------- | ------ |
| **Uno**  | ATmega328P     | 5V       | 32 KB  | 14 (6 PWM)  | 6      |
| **Nano** | ATmega328P     | 5V       | 32 KB  | 14 (6 PWM)  | 8      |
| **Mega** | ATmega2560     | 5V       | 256 KB | 54 (15 PWM) | 16     |

### B. Spesifikasi Arduino Uno

| Spesifikasi      | Nilai           |
| ---------------- | --------------- |
| Mikrokontroler   | ATmega328P      |
| Tegangan Operasi | 5V              |
| Digital I/O Pins | 14 (6 PWM)      |
| Analog Input     | 6 pins (10-bit) |
| Flash Memory     | 32 KB           |
| Clock Speed      | 16 MHz          |

---

## 2. 📝 Struktur Program Arduino

```cpp
/*
 * Program pertama: LED Blink
 * Komentar multi-baris
 */

// Komentar satu baris

void setup() {
  // Dijalankan sekali di awal
  pinMode(13, OUTPUT);    // Set pin 13 sebagai OUTPUT
  Serial.begin(9600);     // Mulai komunikasi serial
}

void loop() {
  // Dijalankan berulang terus
  digitalWrite(13, HIGH); // Nyalakan LED
  delay(1000);             // Tunggu 1 detik
  digitalWrite(13, LOW);  // Matikan LED
  delay(1000);             // Tunggu 1 detik
}
```

> **💡 Penjelasan:**
> - `setup()` : Dijalankan sekali (inisialisasi)
> - `loop()` : Dijalankan berulang (program utama)
> - `pinMode()` : Mengatur mode pin (INPUT/OUTPUT)
> - `digitalWrite()` : Menulis HIGH/LOW ke pin
> - `delay()` : Menunda program (milidetik)

---

## 3. 📊 Variabel dan Tipe Data

### A. Tipe Data Dasar

| Tipe Data | Ukuran   | Rentang               | Contoh                  |
| --------- | -------- | --------------------- | ----------------------- |
| boolean   | 1 byte   | true/false            | `boolean flag = true;`  |
| char      | 1 byte   | -128 s/d 127          | `char huruf = 'A';`     |
| int       | 2 byte   | -32,768 s/d 32,767    | `int angka = 1000;`     |
| float     | 4 byte   | ±3.4E-38 s/d ±3.4E+38 | `float suhu = 26.5;`    |
| String    | variabel | teks                  | `String nama = "Budi";` |

> **💡 Aturan Penamaan:** Case-sensitive, huruf/underscore di awal, tidak boleh pakai kata kunci (if, else, for, dll)

---

## 4. ➗ Operator

### Aritmatika
- `+` (penjumlahan)
- `-` (pengurangan)
- `*` (perkalian)
- `/` (pembagian)
- `%` (modulus/sisa bagi)

### Perbandingan
- `==` (sama dengan)
- `!=` (tidak sama)
- `<` (kurang dari)
- `>` (lebih dari)
- `<=` (kurang sama)
- `>=` (lebih sama)

### Logika
- `&&` (AND)
- `||` (OR)
- `!` (NOT)

### Increment
- `i++` (post-increment)
- `++i` (pre-increment)
- `i--` (post-decrement)
- `--i` (pre-decrement)

---

## 5. 🔀 Struktur Kontrol

### A. if-else

```cpp
int nilai = 85;

if (nilai >= 60) {
  Serial.println("Lulus");
} else {
  Serial.println("Tidak Lulus");
}
```

### B. switch-case

```cpp
int hari = 3;
String namaHari;

switch (hari) {
  case 1: namaHari = "Senin"; break;
  case 2: namaHari = "Selasa"; break;
  case 3: namaHari = "Rabu"; break;
  default: namaHari = "Tidak valid"; break;
}
```

### C. Perulangan (Loop)

```cpp
// for loop
for (int i = 1; i <= 5; i++) {
  Serial.println(i);
}

// while loop
int i = 1;
while (i <= 5) {
  Serial.println(i);
  i++;
}
```

---

## 6. 📋 Array

### A. Deklarasi

```cpp
// Array 1 dimensi
int nilai[5];                 // 5 elemen
int angka[3] = {10, 20, 30};  // inisialisasi

// Array 2 dimensi
int matrix[2][3] = {
  {1, 2, 3},
  {4, 5, 6}
};
```

---

## 7. 🎯 Built-in Functions Arduino

### I/O Digital

```cpp
pinMode(13, OUTPUT);
digitalWrite(13, HIGH);
int nilai = digitalRead(2);
```

### I/O Analog

```cpp
int nilai = analogRead(A0);
analogWrite(9, 128); // PWM
```

### Waktu

```cpp
delay(1000);
unsigned long waktu = millis();
```

### Serial

```cpp
Serial.begin(9600);
Serial.println("Hello");
```

---

## 8. 📝 Program Contoh dari Simulasi

### LED dengan Button

```cpp
const int led = 13;
const int button = 2;
int state = 0;

void setup() {
  pinMode(led, OUTPUT);
  pinMode(button, INPUT);
}

void loop() {
  state = digitalRead(button);
  if (state == HIGH) {
    digitalWrite(led, HIGH);
  } else {
    digitalWrite(led, LOW);
  }
}
```

### Running LED

```cpp
int pins[] = {2,3,4,5,6,7,8,9};

void setup() {
  for(int i=0; i<8; i++) {
    pinMode(pins[i], OUTPUT);
  }
}

void loop() {
  for(int i=0; i<8; i++) {
    digitalWrite(pins[i], HIGH);
    delay(100);
    digitalWrite(pins[i], LOW);
  }
}
```

### Animasi LED

```cpp
int led[] = {2,3,4,5,6,7,8,9};
int pola[][4] = {
  {1,3,5,7}, {2,4,6,8}
};

void loop() {
  for(int i=0; i<2; i++) {
    for(int j=0; j<4; j++) {
      digitalWrite(led[pola[i][j]], HIGH);
    }
    delay(200);
    for(int j=0; j<4; j++) {
      digitalWrite(led[pola[i][j]], LOW);
    }
  }
}
```

---

## 📚 Sumber Belajar

- 📄 [Artikel Medium - Dasar Pemrograman Arduino C++](https://medium.com/@ikhwanfathulloh/pemograman-dasar-arduino-bbec7bb6ec11)
- 🎥 [Playlist YouTube - Tutorial Pemrograman Arduino untuk Pemula](https://www.youtube.com/playlist?list=PLaC2GD6EmthV7dJBrq9eMw0NNCk83fLxs)
