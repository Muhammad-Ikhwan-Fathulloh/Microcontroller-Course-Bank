# Pertemuan 9: 🏛️ Fondasi Mikroprosesor dan Algoritma

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami arsitektur dasar mikroprosesor
2. Menjelaskan fungsi Register dan ALU (*Arithmetic Logic Unit*)
3. Memahami peran Status Flags dalam eksekusi instruksi
4. Menganalisis algoritma dasar pemrosesan data pada level rendah

---

## 📚 Materi Pokok

### 1. Arsitektur Mikroprosesor

#### A. Komponen Utama
Mikroprosesor adalah "otak" dari sistem komputer yang terdiri dari tiga bagian utama:
1. **Control Unit (CU):** Mengatur jalannya instruksi dan sinkronisasi komponen.
2. **Arithmetic Logic Unit (ALU):** Melakukan operasi perhitungan matematis dan logika.
3. **Registers:** Lokasi penyimpanan internal yang sangat cepat untuk data sementara.

#### B. Siklus Instruksi (Fetch-Decode-Execute)
Setiap instruksi yang dijalankan mikroprosesor melalui siklus:
1. **Fetch:** Mengambil instruksi dari memori.
2. **Decode:** Menerjemahkan instruksi menjadi sinyal kontrol.
3. **Execute:** Menjalankan operasi yang diminta.

---

### 2. Register dan Status Flags

#### A. Jenis Register
- **Accumulator:** Digunakan untuk menampung hasil operasi ALU.
- **Program Counter (PC):** Menunjuk ke alamat instruksi selanjutnya.
- **Stack Pointer (SP):** Mengelola tumpukan memori (stack).
- **General Purpose Registers:** Penyimpanan data umum.

#### B. Status Flags (Condition Code)
Flags adalah bit-bit status yang mencerminkan hasil operasi terakhir di ALU:
- **Zero Flag (Z):** Bernilai 1 jika hasil operasi adalah nol.
- **Carry Flag (C):** Bernilai 1 jika terjadi bawaan (carry) dari bit paling signifikan.
- **Sign Flag (S):** Menunjukkan hasil positif (0) atau negatif (1).
- **Overflow Flag (V):** Terjadi jika hasil operasi melebihi kapasitas register.

---

### 3. Struktur Algoritma Dasar

Pada level mikroprosesor, algoritma diimplementasikan melalui instruksi dasar seperti:
1. **Data Transfer:** `MOV`, `PUSH`, `POP`.
2. **Arithmetic:** `ADD`, `SUB`, `INC`, `DEC`.
3. **Logic:** `AND`, `OR`, `XOR`, `NOT`.
4. **Control Flow:** `JMP`, `JZ` (Jump if Zero), `CALL`, `RET`.

---

### 4. Perbandingan Mikroprosesor vs Mikrokontroler

| Fitur             | Mikroprosesor     | Mikrokontroler                     |
| ----------------- | ----------------- | ---------------------------------- |
| **Integrasi**     | Hanya CPU         | CPU, RAM, ROM, I/O dalam satu chip |
| **Penggunaan**    | Komputer / Server | Sistem Tertanam (Embedded)         |
| **Harga**         | Mahal             | Murah                              |
| **Konsumsi Daya** | Tinggi            | Rendah                             |

---

## 📚 Sumber Belajar

- 📁 [Repositori GitHub - Microprocessor Course Bank](https://github.com/Muhammad-Ikhwan-Fathulloh/Microprocessor-Course-Bank)
- 📄 [GeeksforGeeks - Microprocessor Architecture](https://www.geeksforgeeks.org/architecture-of-microprocessor/)
- 📘 [Buku Referensi: Microprocessor Architecture, Programming, and Applications with the 8085](https://www.amazon.com/Microprocessor-Architecture-Programming-Applications-8085/dp/8185010642)
