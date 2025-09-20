# MQTT 
=========================================================

Prasyarat singkat
- Jalankan broker MQTT (service `mqtt-broker` di docker-compose) sebelum subscriber/publisher.

Alur 
1. Publisher (`pub.py`) membuat klien MQTT dan memanggil `client.connect(broker, port, keepalive=60)` untuk tersambung ke broker.
2. Setelah koneksi berhasil, publisher memasuki loop yang pada setiap iterasi memanggil `client.publish(topic, message)` untuk mengirim pesan ke topik `sister/temp` lalu `time.sleep(1)`.
3. Subscriber (`sub.py`) membuat klien MQTT, memanggil `client.connect(broker, port, keepalive=60)`, lalu `client.subscribe(topic)` untuk mendaftar topik.
4. Subscriber menjalankan `client.loop_forever()` sehingga callback `on_message` dipanggil setiap broker mendorong pesan ke client. Di dalam `on_message`, program menjalankan `print(message.payload.decode())` untuk menampilkan pesan.

Baris kode yang dieksekusi (potongan yang penting)
- `pub.py` (konstruk utama):
```python
client = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
client.connect(broker, port, keepalive=60)
while True:
	message = f"Suhu: {suhu}\u00b0C"
	client.publish(topic, message)   # <- baris yang mengirim pesan ke broker
	time.sleep(1)
```

- `sub.py` (konstruk utama):
```python
client = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
client.on_message = on_message
client.connect(broker, port, keepalive=60)
client.subscribe(topic)             # <- baris yang mendaftarkan topik
client.loop_forever()               # <- baris yang menjaga loop dan memicu callback

def on_message(client, userdata, message, properties=None):
	print(message.payload.decode()) # <- baris yang menampilkan payload saat pesan diterima
```

output yang dihasilkan
https://imgur.com/gallery/mqtt-6O9yd7t#DMJBeoK
- Terminal publisher (`pub.py`) saat berjalan:
```
Menghubungkan ke mqtt-broker...
Published: Suhu: 28°C
Published: Suhu: 28°C
Published: Suhu: 28°C
```
https://imgur.com/gallery/mqtt-6O9yd7t#Ot7ZywV
- Terminal subscriber (`sub.py`) saat berjalan:
```
Menghubungkan ke mqtt-broker...
Berlangganan topik: sister/temp
Suhu: 28°C
Suhu: 28°C
Suhu: 28°C
```

Catatan operasional singkat
- Jalankan broker dulu (docker compose), kemudian subscriber, terakhir publisher agar subscriber tidak kehilangan pesan awal.
- Jika ingin mengubah alamat broker, ubah variabel `broker` di kedua file.

Kemudian berikut hasil Wireshark 
https://imgur.com/gallery/mqtt-6O9yd7t#BMGhJTV


