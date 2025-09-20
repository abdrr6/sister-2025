# MQTT 
Terdapat dua program—publisher (pub.py) yang terus mengirim pesan suhu, dan subscriber (sub.py) yang berlangganan topik dan menampilkan pesan yang masuk.


Broker MQTT (servis mqtt-broker di docker-compose) adalah perantara. Publisher tidak mengirim langsung ke subscriber, tapi ke broker.
pub.py membuat koneksi ke broker lalu tiap 1 detik mem-publish pesan ke topik sister/temp.
sub.py membuat koneksi ke broker, subscribe ke topik yang sama, lalu menunggu. Ketika broker menerima pesan, broker meneruskan pesan ke subscriber, lalu sub.py menampilkan isi pesan.
Baris kode yang paling berfungsi (dari pub.py):

client creation & connect:
client = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
client.connect(broker, port, keepalive=60)
publish loop:
client.publish(topic, message)
time.sleep(1)
Baris kode yang paling berfungsi (dari sub.py):

client creation & connect:
client = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
client.connect(broker, port, keepalive=60)
subscribe & message handling:
client.subscribe(topic)
def on_message(...): print(f"Received message: {message.payload.decode()} (Topic: {message.topic})")
client.loop_forever()
Output yang muncul ketika berjalan:

Publisher prints:
"Published: Suhu: 28°C"
Subscriber prints:
"Received message: Suhu: 28°C (Topic: sister/temp)"
