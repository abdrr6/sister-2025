# REST (Flask) 
=============================================

Deskripsi program
Merupakan API REST sederhana menggunakan Flask. Server (`server.py`) menyediakan dua endpoint HTTP:
- `/add`: menerima query parameter `a` dan `b`, mengembalikan hasil penjumlahan sebagai JSON.
- `/mul`: menerima query parameter `a` dan `b`, mengembalikan hasil perkalian sebagai JSON.

Client (`client.py`) menggunakan library `requests` untuk memanggil endpoint tersebut, membaca respons JSON, lalu mencetak hasil ke terminal. Tujuan demo: memperlihatkan alur request → proses di server → response JSON → parsing di client.

<img src="https://i.imgur.com/ZWBkWiR.png" width="400">


Langkah eksekusi
1. Jalankan `server.py`, Flask app bind ke `0.0.0.0:5151` dengan endpoint `/add` dan `/mul`.
2. Jalankan `client.py` yang memanggil `requests.get(f"{BASE}/{endpoint}", params={'a': a, 'b': b})` ke server.
3. Server mengambil parameter `a` dan `b`, menghitung hasil, lalu `return jsonify({'result': result})`.
4. Client membaca JSON respons dan mencetak hasil ke terminal.

Kode yang dieksekusi 
```python
# server.py (Flask)
@app.route('/add', methods=['GET'])
def sub_numbers():
    a = int(request.args.get('a'))
    b = int(request.args.get('b'))
    return jsonify({'result': a + b})  # <- mengembalikan hasil sebagai JSON
```
<img src="https://i.imgur.com/RBOUYVZ.png" width="400">

```python
# client.py
r = requests.get(f"{BASE}/{endpoint}", params={'a': a, 'b': b}, timeout=3)
data = r.json()
print(f"{endpoint}({a},{b}) = {data['result']}")  # <- menampilkan hasil
```
<img src="https://i.imgur.com/TI6mzan.png" width="400">

Contoh output client
```
add(10,5) = 15
mul(10,5) = 50
```
Hasil capture dengan Wireshark

<img src="https://i.imgur.com/a16o4Am.png">
