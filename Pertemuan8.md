# Pertemuan 8: ↔️ Protokol Komunikasi: MQTT

> **Mikrokontroler | Teknik Informatika**

---

## 🎯 Tujuan Pembelajaran

Setelah mengikuti pertemuan ini, mahasiswa diharapkan mampu:

1. Memahami konsep protokol MQTT (*Message Queuing Telemetry Transport*)
2. Menjelaskan arsitektur *Publish/Subscribe*
3. Menggunakan ESP32 sebagai MQTT Client untuk komunikasi M2M
4. Membandingkan efisiensi MQTT dengan HTTP untuk perangkat IoT

---

## 📚 Materi Pokok

### 1. Mengenal MQTT

#### A. Apa itu MQTT?
MQTT adalah protokol messaging yang sangat ringan yang dirancang untuk perangkat dengan sumber daya terbatas (low-bandwidth, high-latency). MQTT bekerja di atas protokol TCP/IP.

#### B. Arsitektur Publish/Subscribe
Berbeda dengan HTTP yang menggunakan model *Request/Response*, MQTT menggunakan model *Publish/Subscribe*.

- **Broker:** Server pusat yang mengatur distribusi pesan (contoh: Mosquitto, HiveMQ, EMQX).
- **Publisher:** Perangkat yang mengirim pesan ke topik tertentu (contoh: Sensor Suhu).
- **Subscriber:** Perangkat yang menerima pesan dari topik tertentu (contoh: Display Dashboard).
- **Topic:** Alamat filter pesan (contoh: `rumah/kamar1/lampu`).

---

### 2. Keunggulan MQTT vs HTTP

| Fitur              | HTTP                        | MQTT                                |
| ------------------ | --------------------------- | ----------------------------------- |
| **Model**          | Request / Response          | Publish / Subscribe                 |
| **Overhead**       | Besar (Header teks)         | Sangat Kecil (Binary 2-byte header) |
| **Efisiensi Daya** | Kurang (Koneksi buka/tutup) | Sangat Baik (Koneksi tetap terbuka) |
| **Kecepatan**      | Lambat                      | Sangat Cepat (Real-time)            |

---

### 3. ESP32 sebagai MQTT Client

Untuk menggunakan MQTT pada ESP32, library yang paling umum digunakan adalah `PubSubClient`.

#### A. Contoh Kode: Publish & Subscribe

```cpp
#include <WiFi.h>
#include <PubSubClient.h>

const char* ssid = "NamaWiFi";
const char* password = "PasswordWiFi";
const char* mqtt_server = "broker.hivemq.com";

WiFiClient espClient;
PubSubClient client(espClient);

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Pesan masuk [");
  Serial.print(topic);
  Serial.print("]: ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();
}

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) { delay(500); }
  
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
}

void reconnect() {
  while (!client.connected()) {
    if (client.connect("ESP32Client_UniqueName")) {
      client.subscribe("rumah/lampu1");
    } else {
      delay(5000);
    }
  }
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
  
  // Publish data setiap 5 detik
  static unsigned long lastMsg = 0;
  if (millis() - lastMsg > 5000) {
    lastMsg = millis();
    client.publish("rumah/suhu", "27.5");
  }
}
```

---

### 4. Quality of Service (QoS)

MQTT memiliki tiga tingkat jaminan pengiriman pesan:
1. **QoS 0 (At most once):** Pesan dikirim sekali, tidak ada jaminan sampai.
2. **QoS 1 (At least once):** Pesan dijamin sampai setidaknya satu kali.
3. **QoS 2 (Exactly once):** Pesan dijamin sampai tepat satu kali (paling aman tapi paling lambat).

---

## 🎥 Video Penjelasan

### Dasar Komunikasi Data MQTT

<iframe width="725" height="453" src="https://www.youtube.com/embed/5j466VQMZO4" frameborder="0" allowfullscreen></iframe>

---

## 📚 Sumber Belajar

- 📁 [Proyek GitHub - Smarthome dengan MQTT](https://github.com/Muhammad-Ikhwan-Fathulloh/Smarthome-MQTT)
- 🌐 [Official MQTT Website](https://mqtt.org/)
- 📄 [Random Nerd Tutorials - ESP32 MQTT Guide](https://randomnerdtutorials.com/esp32-mqtt-publish-subscribe-arduino-ide/)
