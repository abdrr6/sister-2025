
SOAP 
=====================================

Langkah eksekusi
1. Jalankan `server.py`, Spyne membuat layanan SOAP dan mengekspor WSDL pada `http://0.0.0.0:8000/?wsdl`.
2. `client.py` membuat `Client(wsdl=wsdl)` dari Zeep, yang mengambil WSDL dari server.
3. Client memanggil `client.service.add(10, 5)`, server menjalankan method `add` dan mengembalikan hasil.

<img src="https://i.imgur.com/BHSPt71.png">

Potongan kode yang dieksekusi
```python
# server.py (Spyne)
class CalculatorService(ServiceBase):
	@rpc(Integer, Integer, _returns=Integer)
	def add(ctx, a, b):
		return a + b  # <- method yang dipanggil lewat SOAP

# client.py (Zeep)
client = Client(wsdl=wsdl)
result = client.service.add(10, 5)  # <- memanggil service SOAP
print(result)
```
<img src="https://i.imgur.com/CQw3NQn.png">

Contoh output client
```
Hasil penjumlahan dari server SOAP: 15
```
<img src="https://i.imgur.com/9uuMr7s.png">

Hasil Capture lalu lintas data dengan Wireshark
<img src="https://i.imgur.com/f8mjKol.png">


<img src="https://i.imgur.com/NskOKp3.png">

