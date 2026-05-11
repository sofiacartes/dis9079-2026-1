# sesion-08

lunes 27 abril 2026

nos vemos a la vuelta del receso

## Clase

### Uso de CircuitPython en Raspberry Pi Pico 2W:

- CircuitPython: versión simplificada de Python orientada a microcontroladores
- Descargaremos circuitpython 10.2.0 para Raspberry Pi Pico 2W, archivo uf2

mi compañero Tomás en su computador lo instaló para formatear la raspberry y quedó instalado todo correcto!

### Uso de Putty para establecer conexión serial entre el computador y la Raspberry

instalar putty, abrir y configurar para poder abrir la terminal

Configuración:

- modo SERIAL
- puerto COM correspondiente (revisar en arduino)
- velocidad 115200 baud

acá probamos el código que estamos usando en VSCode en conjunto con Putty, abriendo el code de la carpeta de la Raspberry y debemos ver el error que nos tira, es porque nos falta instalar bibliotecas:

- minimqtt
- ticks.mpy
- manager.mpy

```cpp
import time
import board
import analogio
import wifi
import socketpool
import adafruit_minimqtt.adafruit_minimqtt as MQTT

# WiFi
wifi.radio.connect("NOMBREWIFI", "CLAVEWIFI")

# MQTT
pool = socketpool.SocketPool(wifi.radio)
mqtt = MQTT.MQTT(
    broker="io.adafruit.com",
    username="blabla",
    password="blabla",
    socket_pool=pool,
)

mqtt.connect()

# Potentiometer
pot = analogio.AnalogIn(board.A0)

while True:
    value = pot.value * 1023 // 65535
    print(value)

    mqtt.publish("udpmontoyamoraga/feeds/potenciometro", str(value))

    time.sleep(5)
```
### Conectar potenciómetro a Raspberry

Usaremos las 3 patitas del pote, conectadas así:

patita 1: GND
patita 2: ADC0
patita 3: 3V3

ya con eso conectado volvemos a probar el código para ver si se envía a la nube, pero está colapsado :c 



