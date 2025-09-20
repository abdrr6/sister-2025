
oneway (UDP) 
================================================

<img src="https://i.imgur.com/kSy55IK.png">


Langkah eksekusi (step-by-step)
1. Jalankan `serverUDP.py` pada alamat `0.0.0.0:12345` dengan `server_socket.bind(server_address)`.
2. Jalankan `clientUDP.py` yang membuat socket UDP dan memanggil `client_socket.sendto(message.encode(...), server_address)` ke `('udp-server', 12345)`.
3. Server menerima paket lewat `data, client_address = server_socket.recvfrom(1024)` dan (opsional) mengirim balasan dengan `server_socket.sendto(message.encode('utf-8'), client_address)`.

Potongan kode yang dieksekusi (inti)
```python
# serverUDP.py
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_socket.bind(('0.0.0.0', 12345))
data, client_address = server_socket.recvfrom(1024)  # <- menerima data
server_socket.sendto(message.encode('utf-8'), client_address)  # <- mengirim balasan (opsional)
```

<img src="https://i.imgur.com/dBGIJJe.png">

```python
# clientUDP.py
client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_address = ('udp-server', 12345)
client_socket.sendto(message.encode('utf-8'), server_address)  # <- mengirim data
data, server = client_socket.recvfrom(1024)  # <- menerima balasan (jika ada)
```

<img src="https://i.imgur.com/aiXsQGv.png">

Contoh output saat berjalan
- Terminal server:
```
UDP server up and listening on ('0.0.0.0', 12345)
Received message from ('172.18.0.3', 54321): Hello, UDP server!
```
- Terminal client (jika client menunggu balasan):
```
Received from server: Hello, ('0.0.0.0', 12345)
```

Perintah singkat untuk menjalankan
```bash
docker compose -f compose/udp.yml up -d
docker compose -f compose/udp.yml exec udp-server python serverUDP.py
docker compose -f compose/udp.yml exec udp-client python clientUDP.py
```

UDP tidak menjamin pengantaran; tes ini cocok untuk demo sederhana tanpa koneksi yang berat.

Hasil Capture lalu lintas data dengan Wireshark

<img src="https://i.imgur.com/eE0OTsu.png">


