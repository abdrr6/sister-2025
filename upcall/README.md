
Upcall 
=======================================

<img src="https://i.imgur.com/iX4NUYG.png">

Langkah eksekusi
1. Jalankan `servercall.py` yang membuat socket TCP dan `bind(('0.0.0.0', 4141))`, lalu `listen(1)`.
2. Server menunggu koneksi `conn, address = server_socket.accept()`.
3. Jalankan `clientcall.py`, client `connect(('upcall-server', 4141))` lalu `client_socket.send(message.encode())`.
4. Server membaca `data = conn.recv(1024).decode()`, membentuk pesan event `upcall_message = "Upcall event: Processing " + data`, lalu `conn.send(upcall_message.encode())`.
5. Client menerima event dengan `data = client_socket.recv(1024).decode()` dan menampilkannya.

Potongan kode yang dieksekusi
```python
# servercall.py
server_socket = socket.socket()
server_socket.bind(('0.0.0.0', 4141))
server_socket.listen(1)
conn, address = server_socket.accept()
data = conn.recv(1024).decode()
upcall_message = "Upcall event: Processing " + data
conn.send(upcall_message.encode())  # <- kirim event balik ke client
```
<img src="https://i.imgur.com/mVc208G.png">

```python
# clientcall.py
client_socket = socket.socket()
client_socket.connect(('upcall-server', 4141))
client_socket.send(message.encode())              # <- kirim pesan
data = client_socket.recv(1024).decode()          # <- terima event dari server
print('Received upcall from server:', data)
```

<img src="https://i.imgur.com/0ZZeIaz.png">

Contoh output
```
Received upcall from server: Upcall event: Processing Halo
```

<img src="https://i.imgur.com/jwfXpWA.png">

Hasil capture lalu lintas data dengan Wireshark

<img src="https://i.imgur.com/zrfjumf.png">
<img src="https://i.imgur.com/GNfrGFn.png">
<img src="https://i.imgur.com/dlGvSQo.png">
