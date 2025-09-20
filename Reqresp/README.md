
Reqresp (TCP) 
==============================================


Langkah eksekusi
1. Jalankan `server.py`, program membuat socket TCP dan memanggil `server_socket.bind(('0.0.0.0', 2222))` lalu `server_socket.listen(1)`.
2. Server menunggu koneksi `conn, address = server_socket.accept()`.
3. Jalankan `client.py`, client memanggil `client_socket.connect(('reqresp-server', 2222))` lalu mengirim pesan `client_socket.send(message.encode())`.
4. Server membaca pesan `data = conn.recv(1024).decode()` lalu membalas dengan `conn.send(response.encode())`.

<img src="https://i.imgur.com/hrl10Nz.png">

Kode yang dieksekusi
```python
# server.py
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(('0.0.0.0', 2222))
server_socket.listen(1)
conn, address = server_socket.accept()  # <- terima koneksi
data = conn.recv(1024).decode()        # <- baca pesan
conn.send(('Echo: ' + data).encode())  # <- kirim balasan
```

<img src="https://i.imgur.com/AJyB188.png">

```python
# client.py
client_socket = socket.socket()
client_socket.connect(('reqresp-server', 2222))  # <- sambung ke server
client_socket.send(message.encode())             # <- kirim pesan
data = client_socket.recv(1024).decode()         # <- terima balasan
```

<img src="https://i.imgur.com/Ahddwl6.png">

Contoh output
- Terminal server:
```
Server listening on 0.0.0.0:2222
Connection from: ('172.18.0.3', 54321)
Received from client: Halo
```
- Terminal client:
```
Enter message: Halo
Received from server: Echo: Halo
```

Hasil capture lalu lintas data dengan Wireshark

<img src="https://i.imgur.com/kVPB5kT.png">
