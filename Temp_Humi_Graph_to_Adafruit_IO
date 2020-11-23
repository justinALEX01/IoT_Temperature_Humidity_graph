from network import WLAN      # For operation of WiFi network
import time                   # Allows use of time.sleep() for delays
import pycom                  # Base library for Pycom devices
from umqtt import MQTTClient  # For use of MQTT protocol to talk to Adafruit IO
import ubinascii              # Needed to run any MicroPython code
import machine                # Interfaces with hardware components
import micropython            # Needed to run any MicroPython code



from pysense import Pysense
from SI7006A20 import SI7006A20
from LTR329ALS01 import LTR329ALS01

RANDOMS_INTERVAL = 5000 # milisecunde
last_random_sent_ticks = 0  # milisecunde

# Wireless network
WIFI_SSID = "Telekom-7XWcXG"
WIFI_PASS = "hdcda51ckfmb"



# Adafruit IO (AIO)
AIO_SERVER = "io.adafruit.com"
AIO_PORT = 1883
AIO_USER = "alexJustin"
AIO_KEY = "aio_BiXF94oTin7TPML0pcFgCCcNN2X7"
AIO_CLIENT_ID = ubinascii.hexlify(machine.unique_id())
AIO_CONTROL_FEED = "alexJustin/feeds/lights"
AIO_RANDOMS_FEED = "alexJustin/feeds/randoms"
AIO_FEEDS = {'temp': 'alexJustin/feeds/temperature', 'humi': 'alexJustin/feeds/humidity'}

do_temp = True

py = Pysense()

si = SI7006A20(py)
lt = LTR329ALS01(py)

pycom.heartbeat(False)
time.sleep(0.1) # Pentru a nu ramane agatat
pycom.rgbled(0xff0000)  # Status rosu = nu lucreaza


wlan = WLAN(mode=WLAN.STA)
wlan.connect(WIFI_SSID, auth=(WLAN.WPA2, WIFI_PASS), timeout=5000)

while not wlan.isconnected():    # Asteapta conectarea la Wi-Fi
    machine.idle()

print("Connected to Wifi")
pycom.rgbled(0xffd7000) Status portocaliu: Programul lucreaza partial


# Trimite temperatura si umiditatea la Adafruit
def send_sensors():
    temp = si.temperature()
    humi = si.humid_ambient(temp)
    global do_temp
    try:
        if do_temp:
            client.publish(topic=AIO_FEEDS['temp'], msg=str(temp))  # Trimite la Adafruit IO
            print("Temp sent")
        else:
            client.publish(topic=AIO_FEEDS['humi'], msg=str(humi))  # Trimite la Adafruit IO
            print("Humi sent")
        do_temp = not do_temp
    except Exception as e:
        print("FAILED")

# Foloseste protocolul MQTT pentru a se conecta la Adafruit IO
client = MQTTClient(AIO_CLIENT_ID, AIO_SERVER, AIO_PORT, AIO_USER, AIO_KEY)

client.connect()    # Se conecteaza la Adafruit IO folosind MQTT


pycom.rgbled(0x00ff00)


while 1:
    client.check_msg()

    send_sensors()      # Trimite temperatura si umiditate catre Adafruit IO
    for x in range (0, 5):
        time.sleep_ms(1000)
        blue, red = lt.light()
        print('{:5}-Blue  {:5}-Red    Range(0-65535)'.format(blue, red))



client.disconnect()
client = None
wlan.disconnect()
wlan = None
pycom.rgbled(0x000022)# Ledul se activeaza in culoara albastru : stop
print("Disconnected from Adafruit IO.")
