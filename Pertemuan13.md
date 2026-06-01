# Pertemuan 13: 🔐 Keamanan Komunikasi Data IoT (MQTT) - Panduan Lengkap

## 📋 Daftar Isi
1. [Pendahuluan & Latar Belakang](#pendahuluan)
2. [Teori Kriptografi untuk IoT](#teori)
3. [Persiapan Lingkungan Pengujian dengan Shiftr.io](#persiapan)
4. [Implementasi ESP32 (Arduino) - Step by Step](#esp32)
5. [Implementasi Dashboard Web (JavaScript) - Step by Step](#web)
6. [Pengujian dan Verifikasi](#pengujian)
7. [Referensi dan Sumber Valid](#referensi)

---

## 1. Pendahuluan & Latar Belakang <a name="pendahuluan"></a>

### 1.1. Kerentanan MQTT

Protokol MQTT (Message Queuing Telemetry Transport) secara default **tidak menyediakan enkripsi** untuk payload pesan . Data dikirimkan dalam bentuk *plain text*, sehingga siapa pun yang berada dalam jaringan yang sama dapat melakukan *sniffing* menggunakan tools seperti Wireshark atau MQTT Explorer untuk melihat data sensor dan perintah yang dikirimkan.

### 1.2. Mengapa AES?

**Advanced Encryption Standard (AES)** adalah standar enkripsi simetris yang ditetapkan oleh NIST (National Institute of Standards and Technology) dalam publikasi **FIPS PUB 197** . AES dipilih karena:
- **Kecepatan tinggi** - ESP32 memiliki akselerator hardware AES yang dapat mengenkripsi dalam waktu **17-18 mikrodetik** per blok 
- **Keamanan teruji** - Digunakan oleh pemerintah AS untuk informasi terklasifikasi
- **Efisiensi** - Cocok untuk perangkat dengan sumber daya terbatas seperti mikrokontroler

---

## 2. Teori Kriptografi untuk IoT <a name="teori"></a>

### 2.1. Mode ECB (Electronic Codebook)

ECB adalah mode operasi AES yang paling sederhana. Setiap blok 16-byte dienkripsi secara independen dengan kunci yang sama .

**Kelemahan ECB**: Pola yang sama pada plaintext akan menghasilkan ciphertext yang sama, sehingga kurang aman untuk data dengan banyak pengulangan.

**Kelebihan ECB**: 
- Tidak memerlukan Initialization Vector (IV)
- Lebih sederhana dan cocok untuk payload IoT yang kecil
- Implementasi lebih ringan

### 2.2. PKCS#7 Padding

Karena AES hanya dapat mengenkripsi data yang panjangnya kelipatan 16 byte, padding diperlukan untuk data dengan panjang variabel .

**Cara kerja PKCS#7**:
- Jika data tersisa `n` byte dari kelipatan 16, tambahkan `n` byte dengan nilai `n`
- Contoh: Data 5 byte → tambahkan 11 byte bernilai 0x0B (11)

```
Data asli (5 byte):    [41][42][43][44][45]
Setelah padding (16):  [41][42][43][44][45][0B][0B][0B][0B][0B][0B][0B][0B][0B][0B][0B]
```

### 2.3. Hex Encoding

Hasil enkripsi AES berupa data biner (0-255) yang tidak dapat langsung dikirim melalui MQTT. Konversi ke **hex string** (2 karakter per byte) memastikan data tetap utuh selama transmisi .

---

## 3. Persiapan Lingkungan Pengujian dengan Shiftr.io <a name="persiapan"></a>

### 3.1. Mengapa Shiftr.io?

Shiftr.io menyediakan:
- **Visualisasi real-time** - Melihat aliran data antar device secara grafis
- **Broker cloud gratis** - Tidak perlu setup server sendiri
- **Dashboard web** - Bisa memantau semua pesan yang masuk

### 3.2. Setup Akun Shiftr.io

1. Buka [https://shiftr.io/try](https://shiftr.io/try)
2. Daftar akun gratis (tersedia pilihan "Sandbox" untuk development)
3. Setelah login, buat **namespace** baru (misal: `kuliah-iot`)
4. Catat kredensial yang diberikan:
   - **Host**: `nama-namespace.cloud.shiftr.io`
   - **Username**: (disediakan)
   - **Password**: (disediakan)

### 3.3. Visualisasi Dashboard

Shiftr.io menyediakan tampilan visual yang menunjukkan setiap client sebagai titik yang terhubung. Pesan yang dikirim akan terlihat mengalir antar titik .

---

## 4. Implementasi ESP32 (Arduino) - Step by Step <a name="esp32"></a>

### 4.1. Library yang Diperlukan

```cpp
#include <WiFi.h>
#include <PubSubClient.h>     // Library MQTT untuk ESP32
#include "mbedtls/aes.h"      // Library AES dari ESP32 (built-in)
#include <string.h>            // Untuk operasi memori
```

### 4.2. Konfigurasi Dasar

```cpp
// === KONFIGURASI WiFi ===
const char* ssid = "NAMA_WIFI_ANDA";
const char* password = "PASSWORD_WIFI";

// === KONFIGURASI MQTT (Shiftr.io) ===
const char* mqtt_server = "kuliah-iot.cloud.shiftr.io";     // Ganti dengan namespace Anda
const char* mqtt_username = "USERNAME_SHIFTR";              // Dari shiftr.io
const char* mqtt_password = "PASSWORD_SHIFTR";              // Dari shiftr.io
const char* mqtt_topic = "sensor/data";

// === KUNCI AES (WAJIB 16 KARAKTER) ===
const char* aesKey = "KunciAES128Bit!!";    // 16 byte (128 bit)
```

### 4.3. Fungsi Padding PKCS#7

Berdasarkan implementasi dari dokumentasi ESP-IDF :

```cpp
/**
 * Melakukan PKCS#7 padding pada data
 * @param input     Data input
 * @param input_len Panjang data input
 * @param output    Buffer output (harus cukup untuk menampung hasil)
 * @return          Panjang data setelah padding, -1 jika error
 */
int pkcs7_pad(const uint8_t *input, int input_len, uint8_t *output) {
    if (input_len < 0) return -1;
    
    // Hitung jumlah byte padding yang diperlukan
    int pad_len = 16 - (input_len % 16);
    int padded_len = input_len + pad_len;
    
    // Salin data asli
    memcpy(output, input, input_len);
    
    // Tambahkan padding: sebanyak pad_len byte dengan nilai pad_len
    memset(output + input_len, pad_len, pad_len);
    
    return padded_len;
}

/**
 * Menghapus PKCS#7 padding setelah dekripsi
 * @param data      Data yang telah didekripsi
 * @param data_len  Panjang data
 * @return          Panjang data setelah padding dihapus, -1 jika padding tidak valid
 */
int pkcs7_unpad(uint8_t *data, int data_len) {
    if (data_len == 0 || data_len % 16 != 0) return -1;
    
    // Nilai padding adalah byte terakhir
    uint8_t pad_len = data[data_len - 1];
    
    // Validasi nilai padding (harus 1-16)
    if (pad_len == 0 || pad_len > 16) return -1;
    if (pad_len > data_len) return -1;
    
    // Verifikasi bahwa semua byte padding konsisten
    for (int i = 0; i < pad_len; i++) {
        if (data[data_len - 1 - i] != pad_len) return -1;
    }
    
    return data_len - pad_len;
}
```

### 4.4. Fungsi Enkripsi AES-128-ECB

Menggunakan mbedtls library yang terintegrasi di ESP32 :

```cpp
/**
 * Enkripsi data dengan AES-128-ECB
 * @param plaintext     Data plaintext
 * @param plaintext_len Panjang plaintext
 * @param key           Kunci AES (16 byte)
 * @param ciphertext    Buffer output untuk ciphertext
 * @param ciphertext_max Kapasitas maksimal buffer
 * @return              Panjang ciphertext (hex string), -1 jika error
 */
int aes_encrypt(const uint8_t *plaintext, int plaintext_len, 
                const uint8_t *key, char *ciphertext, int ciphertext_max) {
    
    // Step 1: Padding
    uint8_t padded[128];  // Sesuaikan ukuran dengan kebutuhan
    int padded_len = pkcs7_pad(plaintext, plaintext_len, padded);
    if (padded_len < 0) return -1;
    
    // Step 2: Inisialisasi context AES
    mbedtls_aes_context aes_ctx;
    mbedtls_aes_init(&aes_ctx);
    
    // Step 3: Set key untuk enkripsi
    int ret = mbedtls_aes_setkey_enc(&aes_ctx, key, 128);  // 128 bit key
    if (ret != 0) {
        mbedtls_aes_free(&aes_ctx);
        return -1;
    }
    
    // Step 4: Enkripsi ECB
    // Parameter: context, mode, panjang, input, output
    ret = mbedtls_aes_crypt_ecb(&aes_ctx, MBEDTLS_AES_ENCRYPT, padded, padded);
    if (ret != 0) {
        mbedtls_aes_free(&aes_ctx);
        return -1;
    }
    
    mbedtls_aes_free(&aes_ctx);
    
    // Step 5: Konversi ke hex string
    for (int i = 0; i < padded_len; i++) {
        sprintf(&ciphertext[i * 2], "%02x", padded[i]);
    }
    
    return padded_len * 2;  // Panjang hex string
}
```

**Catatan Penting**: Kode di atas menggunakan `mbedtls_aes_crypt_ecb` yang hanya mengenkripsi satu blok. Untuk data multi-blok, perlu iterasi:

```cpp
// Untuk data > 16 byte, enkripsi per blok
for (int i = 0; i < padded_len; i += 16) {
    mbedtls_aes_crypt_ecb(&aes_ctx, MBEDTLS_AES_ENCRYPT, padded + i, padded + i);
}
```

### 4.5. Fungsi Dekripsi AES-128-ECB

```cpp
/**
 * Dekripsi data dengan AES-128-ECB
 * @param ciphertext_hex Ciphertext dalam format hex string
 * @param key            Kunci AES (16 byte)
 * @param plaintext      Buffer output untuk plaintext
 * @param plaintext_max  Kapasitas maksimal buffer
 * @return               Panjang plaintext setelah unpadding, -1 jika error
 */
int aes_decrypt(const char *ciphertext_hex, const uint8_t *key,
                uint8_t *plaintext, int plaintext_max) {
    
    // Step 1: Konversi hex ke binary
    int hex_len = strlen(ciphertext_hex);
    if (hex_len % 2 != 0) return -1;
    
    int cipher_len = hex_len / 2;
    if (cipher_len > plaintext_max) return -1;
    
    uint8_t ciphertext[128];
    for (int i = 0; i < cipher_len; i++) {
        sscanf(&ciphertext_hex[i * 2], "%02hhx", &ciphertext[i]);
    }
    
    // Step 2: Inisialisasi context AES
    mbedtls_aes_context aes_ctx;
    mbedtls_aes_init(&aes_ctx);
    
    // Step 3: Set key untuk dekripsi
    int ret = mbedtls_aes_setkey_dec(&aes_ctx, key, 128);
    if (ret != 0) {
        mbedtls_aes_free(&aes_ctx);
        return -1;
    }
    
    // Step 4: Dekripsi ECB
    ret = mbedtls_aes_crypt_ecb(&aes_ctx, MBEDTLS_AES_DECRYPT, ciphertext, plaintext);
    if (ret != 0) {
        mbedtls_aes_free(&aes_ctx);
        return -1;
    }
    
    mbedtls_aes_free(&aes_ctx);
    
    // Step 5: Hapus padding
    return pkcs7_unpad(plaintext, cipher_len);
}
```

### 4.6. Kode Lengkap ESP32

```cpp
#include <WiFi.h>
#include <PubSubClient.h>
#include "mbedtls/aes.h"
#include <string.h>

// === KONFIGURASI ===
const char* ssid = "WIFI_SSID";
const char* wifi_pass = "WIFI_PASSWORD";
const char* mqtt_server = "namespace-anda.cloud.shiftr.io";
const char* mqtt_user = "username_shiftr";
const char* mqtt_pass = "password_shiftr";
const char* mqtt_topic = "sensor/data";

const uint8_t aes_key[16] = "Kunci16ByteAnda!";  // 16 byte key

WiFiClient espClient;
PubSubClient client(espClient);

// === Fungsi Padding (sama seperti di atas) ===
int pkcs7_pad(const uint8_t *input, int input_len, uint8_t *output) { ... }
int pkcs7_unpad(uint8_t *data, int data_len) { ... }

// === Fungsi Enkripsi (sama seperti di atas) ===
int aes_encrypt(const uint8_t *plaintext, int plaintext_len, 
                const uint8_t *key, char *ciphertext, int ciphertext_max) { ... }

// === Koneksi WiFi ===
void setup_wifi() {
    delay(10);
    Serial.println();
    Serial.print("Connecting to ");
    Serial.println(ssid);
    
    WiFi.begin(ssid, wifi_pass);
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }
    Serial.println("\nWiFi connected");
}

// === Callback untuk pesan masuk ===
void callback(char* topic, byte* payload, unsigned int length) {
    Serial.print("Message arrived [");
    Serial.print(topic);
    Serial.print("]: ");
    
    // Copy payload ke string
    char ciphertext_hex[256];
    memcpy(ciphertext_hex, payload, length);
    ciphertext_hex[length] = '\0';
    Serial.println(ciphertext_hex);
    
    // Dekripsi pesan
    uint8_t plaintext[128];
    int plain_len = aes_decrypt(ciphertext_hex, aes_key, plaintext, sizeof(plaintext));
    
    if (plain_len > 0) {
        Serial.print("Decrypted: ");
        for (int i = 0; i < plain_len; i++) {
            Serial.print((char)plaintext[i]);
        }
        Serial.println();
    } else {
        Serial.println("Decryption failed!");
    }
}

// === Kirim pesan terenkripsi ===
void send_encrypted(const char* message) {
    char ciphertext_hex[256];
    int hex_len = aes_encrypt((const uint8_t*)message, strlen(message), 
                               aes_key, ciphertext_hex, sizeof(ciphertext_hex));
    
    if (hex_len > 0) {
        client.publish(mqtt_topic, ciphertext_hex);
        Serial.print("Sent encrypted: ");
        Serial.println(ciphertext_hex);
    } else {
        Serial.println("Encryption failed!");
    }
}

// === Setup ===
void setup() {
    Serial.begin(115200);
    setup_wifi();
    
    client.setServer(mqtt_server, 1883);
    client.setCallback(callback);
    
    while (!client.connected()) {
        if (client.connect("ESP32Client", mqtt_user, mqtt_pass)) {
            Serial.println("MQTT connected");
            client.subscribe(mqtt_topic);
        } else {
            Serial.print("MQTT failed, rc=");
            Serial.print(client.state());
            delay(5000);
        }
    }
}

// === Loop ===
void loop() {
    client.loop();
    
    // Kirim data sensor setiap 10 detik
    static unsigned long lastSend = 0;
    if (millis() - lastSend > 10000) {
        // Contoh data sensor
        char sensor_data[64];
        float suhu = 25.5 + random(-10, 10) / 10.0;
        sprintf(sensor_data, "{\"suhu\":%.1f,\"kelembaban\":%d}", suhu, random(40, 90));
        
        send_encrypted(sensor_data);
        lastSend = millis();
    }
}
```

---

## 5. Implementasi Dashboard Web (JavaScript) - Step by Step <a name="web"></a>

### 5.1. Library yang Diperlukan

Untuk dekripsi di sisi web, kita menggunakan **CryptoJS** library yang mendukung AES-ECB dengan PKCS#7 padding :

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mqtt/4.3.7/mqtt.min.js"></script>
```

### 5.2. Koneksi MQTT ke Shiftr.io

```javascript
// Konfigurasi Shiftr.io
const mqttConfig = {
    host: 'wss://namespace-anda.cloud.shiftr.io:443/mqtt',  // WebSocket Secure
    username: 'username_shiftr',
    password: 'password_shiftr'
};

// Kunci AES (HARUS SAMA dengan ESP32)
const aesKey = "Kunci16ByteAnda!";

// Koneksi MQTT
const client = mqtt.connect(mqttConfig.host, {
    username: mqttConfig.username,
    password: mqttConfig.password
});

client.on('connect', () => {
    console.log('Connected to Shiftr.io');
    client.subscribe('sensor/data');
});

client.on('message', (topic, message) => {
    const ciphertext = message.toString();
    console.log('Received encrypted:', ciphertext);
    
    // Dekripsi
    const decrypted = decryptAES(ciphertext, aesKey);
    console.log('Decrypted:', decrypted);
    
    // Update dashboard
    updateDashboard(decrypted);
});
```

### 5.3. Fungsi Dekripsi AES-ECB dengan CryptoJS

Berdasarkan dokumentasi CryptoJS :

```javascript
/**
 * Dekripsi data dengan AES-128-ECB menggunakan CryptoJS
 * @param {string} ciphertextHex - Ciphertext dalam format hex string
 * @param {string} key - Kunci AES (16 karakter)
 * @returns {string} Plaintext hasil dekripsi
 */
function decryptAES(ciphertextHex, key) {
    try {
        // Konversi hex string ke WordArray (format CryptoJS)
        const ciphertextWordArray = CryptoJS.enc.Hex.parse(ciphertextHex);
        
        // Konversi key ke WordArray
        const keyWordArray = CryptoJS.enc.Utf8.parse(key);
        
        // Dekripsi dengan mode ECB dan padding PKCS7
        const decrypted = CryptoJS.AES.decrypt(
            { ciphertext: ciphertextWordArray },  // Ciphertext dalam WordArray
            keyWordArray,
            {
                mode: CryptoJS.mode.ECB,      // Mode Electronic Codebook
                padding: CryptoJS.pad.Pkcs7    // Padding PKCS#7
            }
        );
        
        // Konversi hasil ke string UTF-8
        return decrypted.toString(CryptoJS.enc.Utf8);
    } catch (error) {
        console.error('Decryption failed:', error);
        return null;
    }
}

/**
 * Enkripsi data dengan AES-128-ECB
 * @param {string} plaintext - Data yang akan dienkripsi
 * @param {string} key - Kunci AES (16 karakter)
 * @returns {string} Ciphertext dalam format hex
 */
function encryptAES(plaintext, key) {
    const keyWordArray = CryptoJS.enc.Utf8.parse(key);
    const encrypted = CryptoJS.AES.encrypt(plaintext, keyWordArray, {
        mode: CryptoJS.mode.ECB,
        padding: CryptoJS.pad.Pkcs7
    });
    
    // Hasil enkripsi dalam bentuk Base64, konversi ke hex
    return encrypted.ciphertext.toString(CryptoJS.enc.Hex);
}
```

### 5.4. Dashboard HTML Lengkap

```html
<!DOCTYPE html>
<html>
<head>
    <title>IoT Dashboard - Secure MQTT</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mqtt/4.3.7/mqtt.min.js"></script>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .container { max-width: 800px; margin: auto; }
        .card { border: 1px solid #ccc; border-radius: 8px; padding: 20px; margin: 10px 0; }
        .suhu { font-size: 48px; color: #e74c3c; }
        .kelembaban { font-size: 48px; color: #3498db; }
        .log { background: #f5f5f5; height: 300px; overflow-y: scroll; font-family: monospace; padding: 10px; }
        .encrypted { color: #e67e22; }
        .decrypted { color: #27ae60; }
        button { padding: 10px 20px; font-size: 16px; cursor: pointer; }
        input { padding: 8px; width: 300px; margin-right: 10px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🔐 IoT Dashboard - Komunikasi Aman</h1>
        
        <div class="card">
            <h2>Data Sensor Real-time</h2>
            <div>
                <span class="suhu" id="suhu">--</span> °C
            </div>
            <div>
                <span class="kelembaban" id="kelembaban">--</span> %
            </div>
        </div>
        
        <div class="card">
            <h2>Kirim Perintah ke ESP32</h2>
            <input type="text" id="commandInput" placeholder="Contoh: LED_ON atau LED_OFF">
            <button onclick="sendCommand()">Kirim (Terenskripsi)</button>
        </div>
        
        <div class="card">
            <h2>Log Pesan</h2>
            <div class="log" id="log"></div>
        </div>
    </div>

    <script>
        // === KONFIGURASI ===
        const mqttHost = 'wss://kuliah-iot.cloud.shiftr.io:443/mqtt';
        const mqttUser = 'USERNAME_SHIFTR';
        const mqttPass = 'PASSWORD_SHIFTR';
        const aesKey = "Kunci16ByteAnda!";  // SAMA dengan ESP32
        
        const client = mqtt.connect(mqttHost, {
            username: mqttUser,
            password: mqttPass
        });
        
        // === FUNGSI DEKRIPSI ===
        function decryptAES(ciphertextHex, key) {
            try {
                const ciphertextWordArray = CryptoJS.enc.Hex.parse(ciphertextHex);
                const keyWordArray = CryptoJS.enc.Utf8.parse(key);
                const decrypted = CryptoJS.AES.decrypt(
                    { ciphertext: ciphertextWordArray },
                    keyWordArray,
                    { mode: CryptoJS.mode.ECB, padding: CryptoJS.pad.Pkcs7 }
                );
                return decrypted.toString(CryptoJS.enc.Utf8);
            } catch (error) {
                console.error('Decryption error:', error);
                return null;
            }
        }
        
        // === FUNGSI ENKRIPSI ===
        function encryptAES(plaintext, key) {
            const keyWordArray = CryptoJS.enc.Utf8.parse(key);
            const encrypted = CryptoJS.AES.encrypt(plaintext, keyWordArray, {
                mode: CryptoJS.mode.ECB,
                padding: CryptoJS.pad.Pkcs7
            });
            return encrypted.ciphertext.toString(CryptoJS.enc.Hex);
        }
        
        // === UPDATE DASHBOARD ===
        function updateDashboard(decryptedData) {
            try {
                const data = JSON.parse(decryptedData);
                if (data.suhu) document.getElementById('suhu').innerText = data.suhu;
                if (data.kelembaban) document.getElementById('kelembaban').innerText = data.kelembaban;
            } catch (e) {
                // Bukan JSON, mungkin perintah atau data lain
            }
        }
        
        // === LOG KE CONSOLE WEB ===
        function addLog(message, type = 'info') {
            const logDiv = document.getElementById('log');
            const entry = document.createElement('div');
            const time = new Date().toLocaleTimeString();
            entry.innerHTML = `[${time}] <span class="${type}">${message}</span>`;
            logDiv.appendChild(entry);
            logDiv.scrollTop = logDiv.scrollHeight;
        }
        
        // === KIRIM PERINTAH ===
        function sendCommand() {
            const command = document.getElementById('commandInput').value;
            if (!command) return;
            
            const encrypted = encryptAES(command, aesKey);
            client.publish('command', encrypted);
            addLog(`Sent (encrypted): ${encrypted}`, 'encrypted');
            addLog(`Sent (decrypted): ${command}`, 'decrypted');
            document.getElementById('commandInput').value = '';
        }
        
        // === MQTT EVENT HANDLER ===
        client.on('connect', () => {
            addLog('Connected to Shiftr.io MQTT broker', 'info');
            client.subscribe('sensor/data');
            client.subscribe('command');
        });
        
        client.on('message', (topic, message) => {
            const ciphertext = message.toString();
            addLog(`Received from ${topic} (encrypted): ${ciphertext.substring(0, 50)}...`, 'encrypted');
            
            const decrypted = decryptAES(ciphertext, aesKey);
            if (decrypted) {
                addLog(`Received from ${topic} (decrypted): ${decrypted}`, 'decrypted');
                
                if (topic === 'sensor/data') {
                    updateDashboard(decrypted);
                }
            } else {
                addLog(`Failed to decrypt message from ${topic}`, 'error');
            }
        });
        
        client.on('error', (err) => {
            addLog(`MQTT Error: ${err}`, 'error');
        });
    </script>
</body>
</html>
```

---

## 6. Pengujian dan Verifikasi <a name="pengujian"></a>

### 6.1. Menggunakan Shiftr.io Dashboard

1. Buka dashboard visual Shiftr.io di `https://namespace-anda.cloud.shiftr.io`
2. Lihat koneksi ESP32 dan Web Client muncul sebagai titik
3. Klik pada titik untuk melihat detail pesan yang dikirim

### 6.2. Uji dengan MQTT Explorer (Verifikasi Keamanan)

Untuk membuktikan bahwa data terenkripsi dengan benar:

1. Download MQTT Explorer (gratis)
2. Koneksi ke broker yang sama
3. Subscribe ke topic `sensor/data`
4. **Amati**: Data yang muncul adalah hex string acak, **bukan** plaintext

**Contoh hasil yang aman:**
```
Sensor Data (ciphertext): d4e9f7b2c1a8e5f3b6d9c2a4f8e1b7d3
Sensor Data (plaintext):  Tidak terbaca!
```

### 6.3. Verifikasi Padding Error

Jika kunci salah, sistem akan menghasilkan **padding error** karena byte terakhir dari hasil dekripsi tidak valid :

```
Decryption failed! (Bad padding)
```

Ini adalah mekanisme keamanan - attacker tidak bisa membedakan antara "kunci salah" dan "data rusak" (dengan probabilitas 1/256 false positive).

---

## 7. Referensi dan Sumber Valid <a name="referensi"></a>

### 7.1. Standar Resmi

| Standar | Judul | Publikasi |
|---------|-------|-----------|
| **FIPS PUB 197** | Advanced Encryption Standard (AES) | NIST, 2001  |
| **NIST SP 800-38A** | Recommendation for Block Cipher Modes of Operation | NIST, 2001  |
| **RFC 2315** | PKCS #7: Cryptographic Message Syntax | IETF, 1998 |

### 7.2. Dokumentasi Library

| Library | Sumber | Keterangan |
|---------|--------|------------|
| **Mbed TLS** | [https://github.com/Mbed-TLS/mbedtls](https://github.com/Mbed-TLS/mbedtls) | Library kriptografi untuk embedded system  |
| **CryptoJS** | [https://code.google.com/archive/p/crypto-js/](https://code.google.com/archive/p/crypto-js/) | Library kriptografi JavaScript  |
| **PubSubClient** | [https://github.com/knolleary/pubsubclient](https://github.com/knolleary/pubsubclient) | MQTT client untuk Arduino |
| **Shiftr.io** | [https://www.shiftr.io/docs/](https://www.shiftr.io/docs/) | Dokumentasi broker MQTT visual  |

### 7.3. Implementasi Referensi

- **ESP32-AES-CBC-LoRa**: [https://github.com/martinius96/AES-CBC-LoRa-ESP32](https://github.com/martinius96/AES-CBC-LoRa-ESP32) - Implementasi AES-CBC pada ESP32 
- **mqtt-examples**: [https://tigoe.github.io/mqtt-examples/](https://tigoe.github.io/mqtt-examples/) - Contoh MQTT dengan berbagai platform 

---

## 📝 Ringkasan Parameter Penting

| Parameter | Nilai | Keterangan |
|-----------|-------|-------------|
| **Algoritma** | AES-128 | 128-bit key |
| **Mode** | ECB | Electronic Codebook |
| **Padding** | PKCS#7 | Untuk data variabel |
| **Key length** | 16 byte (128 bit) | Wajib tepat |
| **Block size** | 16 byte | Fixed |
| **Output format** | Hex string | Untuk MQTT |
| **MQTT port** | 1883 (TCP) / 8883 (TLS) | Shiftr.io support WebSocket |

---

> **Tugas Praktikum:**
> 1. Setup akun Shiftr.io dan catat kredensial
> 2. Upload kode ESP32 dengan mengganti kredensial dan kunci
> 3. Buka dashboard web HTML dan koneksikan ke broker yang sama
> 4. Gunakan MQTT Explorer untuk memverifikasi bahwa data tidak bisa dibaca plaintext
> 5. Catat hasil pengamatan dan kendala yang dihadapi
