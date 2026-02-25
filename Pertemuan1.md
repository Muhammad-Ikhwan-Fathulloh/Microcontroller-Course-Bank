# Pertemuan 1: 🔌 Dasar Elektronika (Arus, Tegangan, dan Daya)

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami konsep dasar arus, tegangan, dan daya listrik
2. Menerapkan Hukum Ohm dalam perhitungan rangkaian sederhana
3. Merangkai dan mensimulasikan rangkaian dioda bridge
4. Menganalisis bentuk gelombang output penyearah gelombang

---

## 📚 Materi Pokok

### 1. Konsep Dasar Kelistrikan

#### A. Arus Listrik (I)

**Definisi:** Arus listrik adalah aliran muatan listrik (elektron) dari potensial tinggi ke potensial rendah melalui suatu konduktor.

**Rumus:**

```
I = Q / t
```

Dimana:
- I = Arus listrik (Ampere)
- Q = Muatan listrik (Coulomb)
- t = Waktu (detik)

**Analogi:** Arus listrik seperti aliran air dalam pipa. Semakin besar aliran, semakin besar arusnya.

#### B. Tegangan Listrik (V)

**Definisi:** Tegangan adalah perbedaan potensial listrik antara dua titik dalam suatu rangkaian. Tegangan mendorong elektron untuk mengalir.

**Rumus:**

```
V = W / Q
```

Dimana:
- V = Tegangan (Volt)
- W = Energi (Joule)
- Q = Muatan (Coulomb)

**Analogi:** Tegangan seperti tekanan air dalam pipa. Semakin tinggi tekanan, semakin kuat dorongan airnya.

#### C. Hambatan Listrik (R)

**Definisi:** Hambatan adalah kemampuan suatu bahan untuk menghambat aliran arus listrik.

**Rumus:**

```
R = ρ × (L / A)
```

Dimana:
- R = Hambatan (Ohm)
- ρ = Resistivitas bahan (Ohm meter)
- L = Panjang konduktor (meter)
- A = Luas penampang (m²)

#### D. Hukum Ohm

**Definisi:** Hukum Ohm menyatakan bahwa arus yang mengalir melalui konduktor berbanding lurus dengan tegangan dan berbanding terbalik dengan hambatan.

**Rumus:**

```
V = I × R
I = V / R
R = V / I
```

#### E. Daya Listrik (P)

**Definisi:** Daya listrik adalah laju energi listrik yang digunakan atau dihasilkan.

**Rumus:**

```
P = V × I
P = I² × R
P = V² / R
```

Dimana:
- P = Daya (Watt)
- V = Tegangan (Volt)
- I = Arus (Ampere)
- R = Hambatan (Ohm)

> **Contoh Perhitungan:**
>
> Sebuah lampu dengan hambatan 100 Ω dihubungkan dengan sumber tegangan 12V. Berapakah arus yang mengalir dan daya yang digunakan?
>
> **Jawab:**
> - I = V / R = 12 / 100 = 0,12 A (120 mA)
> - P = V × I = 12 × 0,12 = 1,44 Watt

---

### 2. Komponen Elektronika Dasar

#### A. Resistor

**Fungsi:** Menghambat arus listrik, membagi tegangan, membatasi arus.

**Simbol:**

```
───▭─── atau ───▮───
```

**Kode Warna Resistor:**

| Warna   | Angka | Pengali | Toleransi |
| ------- | ----- | ------- | --------- |
| Hitam   | 0     | 10⁰     | -         |
| Coklat  | 1     | 10¹     | ±1%       |
| Merah   | 2     | 10²     | ±2%       |
| Orange  | 3     | 10³     | -         |
| Kuning  | 4     | 10⁴     | -         |
| Hijau   | 5     | 10⁵     | ±0.5%     |
| Biru    | 6     | 10⁶     | ±0.25%    |
| Ungu    | 7     | 10⁷     | ±0.1%     |
| Abu-abu | 8     | 10⁸     | -         |
| Putih   | 9     | 10⁹     | -         |
| Emas    | -     | 10⁻¹    | ±5%       |
| Perak   | -     | 10⁻²    | ±10%      |

**Cara Membaca:**
- Gelang 1: Angka pertama
- Gelang 2: Angka kedua
- Gelang 3: Pengali
- Gelang 4: Toleransi

Contoh: **Coklat - Hitam - Merah - Emas** = 1 0 × 10² = 1000 Ω = 1 kΩ ±5%

#### B. Dioda

**Fungsi:** Mengalirkan arus hanya satu arah (forward bias) dan menghambat arah sebaliknya (reverse bias).

**Simbol:**

```
────▷|────
   Anoda Katoda
```

**Karakteristik:**
- **Forward Bias:** Anoda lebih positif dari katoda → arus mengalir
- **Reverse Bias:** Katoda lebih positif dari anoda → arus terhambat
- **Tegangan Maju (Vf):** 0.6-0.7V untuk dioda silikon
- **Tegangan Tembus:** Tegangan reverse maksimum sebelum dioda rusak

#### C. Kapasitor

**Fungsi:** Menyimpan muatan listrik sementara, filter, kopling.

**Simbol:**

```
───||───  (Elektrolit: ───||─── dengan tanda +)
```

**Rumus:**

```
Q = C × V
C = ε × (A / d)
```

Dimana:
- C = Kapasitansi (Farad)
- Q = Muatan (Coulomb)
- V = Tegangan (Volt)
- ε = Permitivitas dielektrik
- A = Luas plat
- d = Jarak antar plat

---

### 3. Rangkaian Dioda Bridge (Penyearah Gelombang)

#### A. Prinsip Kerja

Dioda bridge digunakan untuk mengubah arus bolak-balik (AC) menjadi arus searah (DC) melalui proses penyearahan gelombang penuh.

#### B. Simulasi Interaktif Tinkercad

Di bawah ini adalah simulasi langsung rangkaian dioda bridge yang dapat Anda jalankan dan eksperimen secara real-time:

<iframe width="725" height="453" src="https://www.tinkercad.com/embed/2mhRJJv0s8d?editbtn=1" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

**Cara Menggunakan Simulasi:**

1. **Jalankan Simulasi:** Klik tombol "Start Simulation" di sudut kanan atas
2. **Amati Komponen:** Lihat rangkaian dioda bridge, kapasitor, dan beban LED
3. **Ukur Tegangan:** Gunakan multimeter virtual dengan mengklik probe pada titik pengukuran
4. **Ubah Nilai:** Double-click komponen untuk mengubah nilai (misal kapasitor)
5. **Lihat Gelombang:** Klik "Waveform Generator" untuk melihat bentuk gelombang
6. **Eksperimen:** Coba lepas kapasitor dan lihat perbedaannya

**Fitur yang Bisa Dicoba:**
- ✅ Menambah/mengurangi kapasitor filter
- ✅ Mengubah nilai resistor beban
- ✅ Mengganti dioda dengan tipe berbeda
- ✅ Menambah LED indikator
- ✅ Mengukur tegangan di setiap titik

#### C. Eksperimen yang Disarankan

| Percobaan              | Langkah                                      | Hasil yang Diamati          |
| ---------------------- | -------------------------------------------- | --------------------------- |
| **1. Tanpa Kapasitor** | Nonaktifkan kapasitor (klik kanan → disable) | Output DC berdenyut (100Hz) |
| **2. Kapasitor Kecil** | Ganti kapasitor 10µF                         | Ripple besar, LED redup     |
| **3. Kapasitor Besar** | Ganti kapasitor 1000µF                       | Ripple kecil, LED terang    |
| **4. Beban Berat**     | Turunkan resistor beban ke 100Ω              | Tegangan turun, ripple naik |
| **5. Beban Ringan**    | Naikkan resistor beban ke 10kΩ               | Tegangan naik, ripple turun |

#### D. Pengukuran dengan Multimeter Virtual

```
Titik Ukur A (Input AC):
- Vrms AC = 12V
- Frekuensi = 50Hz
- Bentuk = Sinusoidal

Titik Ukur B (Output DC tanpa kapasitor):
- Vdc rata-rata = 10.8V
- Frekuensi ripple = 100Hz
- Bentuk = Setengah gelombang penuh

Titik Ukur C (Output DC dengan kapasitor):
- Vdc = 15.6V
- Ripple = < 0.5Vpp
- Bentuk = DC hampir rata
```

---

### 5. Video Penjelasan

🔗 [Penjelasan YouTube - Cara Kerja Dioda Bridge](https://www.youtube.com/watch?v=n-bfkUUbT6k)

---

> *"Elektronika bukanlah tentang komponen, tetapi tentang bagaimana komponen bekerja sama untuk menciptakan sesuatu yang bermanfaat."*
