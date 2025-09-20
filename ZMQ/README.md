ZeroMQ (ZMQ) 
=============================================

<img src="https://i.imgur.com/at4IMM4.png">

1) PUB/SUB
- Langkah eksekusi:
  1. Jalankan `pubzmq.py` yang membuat context dan socket PUB, lalu `socket.bind("tcp://*:12345")`.
  2. Jalankan `subzmq.py` yang membuat socket SUB, `socket.connect("tcp://zmq-pub:12345")` dan `socket.setsockopt_string(zmq.SUBSCRIBE, "WAKTU")`.
  3. Publisher memanggil `socket.send_string(message)`; subscriber memanggil `socket.recv_string()` dan menampilkan pesan.

- Potongan kode (PUB):
```python
context = zmq.Context()
socket = context.socket(zmq.PUB)
socket.bind("tcp://*:12345")
socket.send_string(message)  # <- kirim pesan
```

- Potongan kode (SUB):
```python
context = zmq.Context()
socket = context.socket(zmq.SUB)
socket.connect("tcp://zmq-pub:12345")
socket.setsockopt_string(zmq.SUBSCRIBE, "WAKTU")
message = socket.recv_string()  # <- terima pesan
print(message)
```
<img src="https://i.imgur.com/U1lDiVv.png">

- Contoh output PUB/SUB:
```
Published: WAKTU Mon Oct 06 12:00:00 2025
Received: WAKTU Mon Oct 06 12:00:00 2025
```

2) PUSH/PULL
- Langkah eksekusi:
  1. Jalankan `pushzmq.py` yang membuat socket PUSH dan `socket.bind("tcp://*:9999")`.
  2. Jalankan `pullzmq.py` (worker) yang membuat socket PULL dan `socket.connect("tcp://zmq-push:9999")`.
  3. Producer `socket.send(pickle.dumps(workload))`; worker `work = pickle.loads(socket.recv())` lalu memproses work.

- Potongan kode (PUSH):
```python
context = zmq.Context()
socket = context.socket(zmq.PUSH)
socket.bind("tcp://*:9999")
socket.send(pickle.dumps(workload))  # <- kirim pekerjaan
```

- Potongan kode (PULL):
```python
context = zmq.Context()
socket = context.socket(zmq.PULL)
socket.connect("tcp://zmq-push:9999")
work = pickle.loads(socket.recv())  # <- terima pekerjaan
print(f"Worker received: {work}")
```

<img src="https://i.imgur.com/L9vkphc.png">

- Contoh output PUSH/PULL:
```
Produced workload: 42
Worker 1 received work: 42
```

3) REQ/REP
- Langkah eksekusi:
  1. Jalankan `serverzmq.py` (REP) yang `socket.bind("tcp://*:5555")` dan menunggu request `message = socket.recv()` lalu `socket.send(b"World")`.
  2. Jalankan `clientzmq.py` (REQ) yang `socket.connect("tcp://zmq-rep:5555")`, `socket.send(b"Hello")` lalu menerima reply dengan `socket.recv()`.

- Potongan kode (REP):
```python
context = zmq.Context()
socket = context.socket(zmq.REP)
socket.bind("tcp://*:5555")
message = socket.recv()  # <- terima request
socket.send(b"World")   # <- kirim reply
```

- Potongan kode (REQ):
```python
context = zmq.Context()
socket = context.socket(zmq.REQ)
socket.connect("tcp://zmq-rep:5555")
socket.send(b"Hello")   # <- kirim request
message = socket.recv()  # <- terima reply
print(message)
```

<img src="https://i.imgur.com/WvQODyG.png">

- Contoh output REQ/REP:
```
Sending request 0 ...
Received reply 0: b"World"
```

Perintah singkat untuk menjalankan (contoh PUB/SUB):
```bash
docker compose -f compose/zmq.yml up -d
docker compose -f compose/zmq.yml exec zmq-pub python pubzmq.py
docker compose -f compose/zmq.yml exec zmq-sub python subzmq.py
```

Catatan singkat
- Pastikan service names di docker-compose cocok (`zmq-pub`, `zmq-push`, `zmq-rep` kalau dipakai di compose). Jika tidak, gunakan `docker compose exec` untuk masuk ke container dan jalankan script langsung.
ZeroMQ (ZMQ) Demo
==================

Ringkasan
- Contoh beberapa pola komunikasi ZMQ: PUB/SUB, PUSH/PULL, REQ/REP.

Cara kerja umum
- PUB/SUB: Publisher `bind` di port, subscriber `connect` dan `SUBSCRIBE` topik, lalu `recv_string()`.
- PUSH/PULL: Producer `bind` dan `send(pickle.dumps(work))`, worker `connect` dan `recv()`.
- REQ/REP: REQ client `send()` lalu `recv()`; REP server `recv()` lalu `send()`.

Baris kode penting
- `pubzmq.py`: `socket.bind("tcp://*:12345"); socket.send_string(message)`
- `subzmq.py`: `socket.connect("tcp://zmq-pub:12345"); socket.setsockopt_string(zmq.SUBSCRIBE, "WAKTU")`
- `pushzmq.py` / `pullzmq.py`: `socket.bind("tcp://*:9999")` / `socket.connect("tcp://zmq-push:9999")`
- `serverzmq.py` / `clientzmq.py`: `socket.bind("tcp://*:5555")` / `socket.connect("tcp://zmq-rep:5555")`

Contoh output PUB/SUB
```
Published: WAKTU Mon Oct 06 12:00:00 2025
Received: WAKTU Mon Oct 06 12:00:00 2025
```
Hasil Capture lalu lintas data dengan Wireshark
<img src="https://i.imgur.com/DmIOKSt.png">

<img src="https://i.imgur.com/7FJnLEf.png">

