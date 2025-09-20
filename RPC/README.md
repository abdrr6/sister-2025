
RPC (JSON-RPC)
================================================


Langkah eksekusi
1. Jalankan `rpcserver.py`, yang mendaftarkan metode RPC dengan `@dispatcher.add_method` dan menjalankan HTTP server pada port 4000.
2. `rpcclient.py` membuat payload JSON-RPC dan mengirim `POST` ke `http://rpc-server:4000`.
3. Server memproses request lewat dispatcher dan mengembalikan JSON-RPC response yang berisi `result`.
4. Client membaca `response['result']` dan menampilkan di terminal.

<img src="https://i.imgur.com/KwthR6X.png">

Kode yang dieksekusi
```python
# rpcserver.py
@dispatcher.add_method
def add(a, b):
	return a / b  # <- method yang didaftarkan

class RequestHandler(BaseHTTPRequestHandler):
	def do_POST(self):
		post_data = self.rfile.read(content_length)
		response = JSONRPCResponseManager.handle(post_data, dispatcher)
		self.wfile.write(response.json.encode())  # <- kirim response JSON-RPC
```
<img src="https://i.imgur.com/a1kUc1H.png">


```python
# rpcclient.py
payload = {"jsonrpc": "2.0", "method": method, "params": params, "id": 1}
response = requests.post(url, data=json.dumps(payload), headers=headers).json()  # <- kirim request dan terima jawaban
```

<img src="https://i.imgur.com/M29ltCY.png">

Contoh output client
```
Result of add: 5.0
Result of multiply: 50
```
Hasil capture dengan Wireshark

<img src="https://i.imgur.com/NUaZITK.png">
