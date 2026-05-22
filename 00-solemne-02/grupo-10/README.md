# solemne-02

### Integrantes

- Braulio Figueroa / github: [brauliofigueroa2001](https://github.com/brauliofigueroa2001)
- Luisa Toro / github: [Luisaatoro9](https://github.com/Luisaatoro9)
- Marlén Soto / github: [marlensoto-lab](https://github.com/marlensoto-lab)
- Marcela Zúñiga / github: [marcezm](https://github.com/marcezm)
---

### Lista de Materiales — Proyecto Interacción Inalámbrica

**Hardware**

| Componente | Cantidad | Función en el proyecto |
|---|---|---|
| Raspberry Pi Pico 2W | 1 | Emisor — envía el dato a Adafruit IO al presionar el botón |
| Arduino Uno R4 WiFi | 1 | Receptor — recibe el dato desde Adafruit IO y enciende el LED |
| Pulsador (botón) | 1 | Sensor — activa o desactiva el envío de datos como "puerta" |
| LED | 1 | Actuador — confirma visualmente que el dato llegó con éxito |
| Resistencia 220Ω | 1 | Protege el LED limitando la corriente |
| Protoboard | 2 | Una para cada placa — permite armar el circuito sin soldar |
| Cables jumper | varios | Conexiones entre componentes en la protoboard |

**Conectividad**

| Elemento | Detalle |
|---|---|
| Red WiFi 2.4GHz | Hotspot generado desde celular — permite alejar las placas entre sí sin perder conexión |
| Celulares | 2 — cada uno genera su propia red para que cada placa se conecte de forma independiente |
| Adafruit IO | Plataforma en la nube que actúa como intermediario (broker MQTT) entre la Raspberry y el Arduino |

**Software**

| Herramienta | Uso |
|---|---|
| CircuitPython | Lenguaje usado para programar la Raspberry Pi Pico 2W |
| Arduino IDE | Entorno usado para programar el Arduino Uno R4 WiFi |
| Adafruit IO | Dashboard y broker MQTT para visualizar y transmitir los datos |
| PuTTY | Monitor serie para ver en tiempo real lo que hace la Raspberry |

---
### 1. Introducción y Organización

Esta sesión fue el punto de encuentro de todo lo aprendido en el semestre. El objetivo: lograr que una Raspberry Pi Pico 2 W (emisor) controle un LED en un Arduino UNO R4 WiFi (receptor) a través de la nube.

Nos organizamos inicialmente en duplas para asegurar que cada parte funcionara de forma independiente antes de unirlas:

* *Braulio:* Encargado de la lógica de envío, configuración del protocolo MQTT y conexión del sensor en la Raspberry.
* *Luisa:* Encargada de la recepción de datos, el circuito del actuador en Arduino y el debugging de hardware.
* *Marlén y Marcela:* Se integraron al cierre de la sesión para colaborar en las pruebas finales y planificar la escalabilidad del proyecto.
---
### Avance en clases dúo Braulio Figueroa y Luisa toro
---
### Sensor usado - Boton


El objetivo es tener un código de enviar desde un Raspberry Pi Pico 2W y un código de recibir en un Arduino Uno R4 Wifi, utilizaremos un botón como primer acercamiento para poder crear una especie de "puerta" que nos dé la opción de activar y desactivar el envío de lecturas de datos hacia Adafruit IO, de esta manera, el servidor de IO no colapsa y evitamos problemas.

**Conexión de botón a Raspberry Pi Pico 2w**

En un comienzo buscamos ejemplos de conexión de un botón a Raspberry Pi Pico 2w pero en las fotos mostraban la conexión mediante una resistencia, le preguntamos a Aarón si esto era necesario pero nos dijo que no porque estos botones vienen con una resistencia interna.

Luego de esta duda, lo que hicimos fue conectar un botón de 4 pines al módulo de Raspberry Pi Pico 2w, para ello seguimos como guía el pinout de la placa visto anteriormente en clases.

![pinoutRaspi](./imagenes/pinoutRaspi2w.JPG)

**Imagen 01**, *Pinout Raspberry Pi Pico 2w*

**Paso 1: Controlar el envío de datos:**
Se usó un botón físico para decidir cuándo mandar información a Adafruit IO. Así no se satura el canal con datos constantes, el tráfico queda limpio y la conexión se mantiene estable. En el código que nos mandó Mateo, se establece que el botón debe estar conectado al pin GP0, ya que, este pin entiende una lógica de 2 estados, HIGH y LOW, lo cuál sirve para el funcionamiento del botón (presionado, no presionado). La otra conexión que debemos hacer es a GND.

<p align="center">
  <img width="770" height="348" alt="image" src="https://github.com/user-attachments/assets/6881ddfc-664e-4bce-ac3e-c526681bc3ab" />
</p>

<p align="center">
  <em>
    En esta imagen se evidencia la conexión del botón con la Raspberry Pi Pico 2 W.
  </em>
</p>

## Código usado para enviar - Raspberry Pi Pico 2w

```cpp
import time
import board
import digitalio
import wifi
import socketpool
import adafruit_minimqtt.adafruit_minimqtt as MQTT

print("Iniciando programa...")

# -------------------------
# WiFi
# -------------------------
SSID = "auxilio"
PASSWORD = "cabal123"

print("Conectando WiFi...")

try:
    wifi.radio.connect(SSID, PASSWORD)
    print("WiFi conectado")
    print("IP:", wifi.radio.ipv4_address)

except Exception as e:
    print("Error WiFi:")
    print(e)

    while True:
        pass


# -------------------------
# Adafruit IO
# -------------------------
AIO_USERNAME = "udpmontoyamoraga"
AIO_KEY = "clavecredencial"

FEED_BOTON = AIO_USERNAME + "/feeds/boton-prueba-grupo10"

print("Creando conexión MQTT...")

pool = socketpool.SocketPool(wifi.radio)

mqtt = MQTT.MQTT(
    broker="io.adafruit.com",
    username=AIO_USERNAME,
    password=AIO_KEY,
    socket_pool=pool,
)

print("Conectando a Adafruit IO...")

try:
    mqtt.connect()
    print("Conectado a Adafruit IO")

except Exception as e:
    print("Error MQTT:")
    print(e)

    while True:
        pass


# -------------------------
# Botón GP0
# -------------------------
boton = digitalio.DigitalInOut(board.GP0)
boton.direction = digitalio.Direction.INPUT
boton.pull = digitalio.Pull.UP

estado_anterior = True

print("Sistema listo")

# -------------------------
# Loop principal
# -------------------------
while True:

    try:
        mqtt.loop()

        estado_actual = boton.value

        # Detecta transición:
        # sin presionar -> presionado
        if estado_anterior and not estado_actual:

            print("Botón presionado")
            print("Enviando impulso...")

            mqtt.publish(FEED_BOTON, "1")

            print("Impulso enviado")

            # anti-rebote
            time.sleep(0.25)

        estado_anterior = estado_actual

    except Exception as e:
        print("Error:")
        print(e)

    time.sleep(0.02)
```

**agregar explicación breve del código?**

- Después de varios intentos de que la placa se conectara al wifi finalmente lo hizo

- Pulsamos el botón y enviaba datos a Adafruit IO, notamos que tiene un pequeño delay al recibir el dato

- Cabe destacar que Mateo nos ayudó durante este proceso

![datos](./imagenes/adafruitBoton2.JPG)

**Imagen 03**, *recibimiento de datos en Adafruit IO*

![datos](./imagenes/botonRaspifeeds.JPG)

**Imagen 04**, *recibimiento de datos en Adafruit IO*





De momento pudimos prender el led en el arduino mediante el pulso con un botón en raspberry pi 

**borrador de lo que se estaba haciendo el lunes, agregar o quitar cosas**







## Actuador usado - Led

**Paso 1: Validar el hardware primero:**
Montamos el LED con su resistencia de **220Ω** en la protoboard. Primero hicimos una prueba de alimentación directa a 5V para confirmar que el LED encendía, y después una prueba de control con un código de parpadeo en el **pin 13**. Ver que el LED respondía bien fue la señal para avanzar a la parte inalámbrica con confianza.

<p align="center">
  <img width="515" height="600" alt="image" src="https://github.com/user-attachments/assets/3cc83dbb-cc79-4aae-9ffd-962d6fffddac" />
</p>

<p align="center">
  <em>
    Esta imagen muestra la conexión del positivo del LED al pin 5V del Arduino para corroborar el correcto encendido del LED.
  </em>
</p>

<p align="center">
  <img width="315" height="315" alt="image" src="https://github.com/user-attachments/assets/ed620dc2-e814-4bce-b9a1-3d4bfbaab040" />
  <img width="315" height="315" alt="image" src="https://github.com/user-attachments/assets/8e4876e2-d6e3-4979-992b-0810f95a0df1" />
  <img width="315" height="315" alt="image" src="https://github.com/user-attachments/assets/5771b7a4-2d57-4942-b04b-82de08ae5800" />
</p>

<p align="center">
  <em>
    Estas imágenes muestran el proceso de conexión del LED al pin de la placa.
  </em>
</p>

### Código utilizado para la prueba de encendido y apagado en el pin 13 reflejándolo en un led
```cpp
void setup() {

  Serial.begin(115200);

  pinMode(13, OUTPUT);
}

void loop() {

  digitalWrite(13, HIGH);

  Serial.println("LED ENCENDIDO");

  delay(500);

  digitalWrite(13, LOW);

  Serial.println("LED APAGADO");

  delay(500);
}
```
<div align="center"> <video src="https://github.com/user-attachments/assets/24d8ffe0-9134-476e-ae22-cc474dfec71e" width="315" autoplay loop muted playsinline></video> <p><em>Este GIF muestra la prueba realizada en el pin 13, enviando un código de encendido y apagado para corroborar tanto el correcto funcionamiento de la conexión del LED como la recepción del código enviado desde Arduino al pin 13.</em></p> </div> 

## Código usado para recibir - Arduino Uno R4 WiFi
```cpp
#include "AdafruitIO_WiFi.h"

#define IO_USERNAME "TU_USUARIO"
#define IO_KEY "TU_KEY"

#define WIFI_SSID "TU_WIFI"
#define WIFI_PASS "TU_PASSWORD"

AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);

const int ledPin = 13;

AdafruitIO_Feed *botonFeed = io.feed("boton-prueba-grupo10");

void setup() {

  pinMode(ledPin, OUTPUT);

  Serial.begin(115200);

  Serial.print("Conectando a Adafruit IO...");

  io.connect();

  botonFeed->onMessage(handleMessage);

  while(io.status() < AIO_CONNECTED) {

    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.println("¡Arduino Conectado!");
}

void loop() {

  io.run();
}

void handleMessage(AdafruitIO_Data *data) {

  int comando = data->toInt();

  if (comando == 1) {

    digitalWrite(ledPin, HIGH);

    Serial.println("LED ON");
  }

  else {

    digitalWrite(ledPin, LOW);

    Serial.println("LED OFF");
  }
}
```



## Imágenes del proyecto
<img width="1440" height="800" alt="image" src="https://github.com/user-attachments/assets/a267f224-6e41-458b-abe8-11420833df01" />

Para documentar cómo logramos la comunicación entre dispositivos de distinta arquitectura, desarrollamos este diagrama que detalla el camino que recorre la información desde la intención del usuario hasta la respuesta física:

1. Nodo Emisor (Raspberry Pi Pico 2 W - CircuitPython) El proceso inicia en la Raspberry Pi, donde el pin GP0 está configurado con una resistencia Pull-UP interna, manteniendo un estado de reposo en True. Al presionar el botón, el código detecta la transición hacia False (bajada a tierra).
   
* *Filtro Crítico:* Antes de enviar cualquier dato, el sistema ejecuta un time.sleep(0.25). Este es nuestro anti-rebote por software, esencial para ignorar el ruido mecánico de las chapitas del botón y asegurar que solo se publique un mensaje limpio por cada presión real.
* *Acción:* Una vez validado, el código ejecuta mqtt.publish, lanzando un "1" hacia la nube.

2. El Puente de Datos (Adafruit IO) El feed compartido, llamado boton-prueba-grupo10, actúa como el punto de encuentro o "puente". Es fundamental entender que la Raspberry y el Arduino no están conectados entre sí, ambos están conectados a este Broker MQTT. El feed recibe el impulso y lo mantiene disponible para cualquier dispositivo que esté escuchando.

3. Nodo Receptor (Arduino UNO R4 WiFi - C++) El Arduino se mantiene en un estado de escucha activa mediante la función io.run(). Está suscrito específicamente a ese feed compartido.

* *Interpretación:* Cuando Adafruit IO notifica que llegó un dato, el Arduino activa la función handleMessage.
* *Validación y Actuación:* El sistema convierte el dato recibido en un entero y pregunta: ¿Es un 1?. Si la respuesta es positiva, se gatilla el comando digitalWrite(13, HIGH).

Conclusión del diagrama: Esta estructura demuestra nuestra capacidad para integrar dos lenguajes de programación distintos (Python y C++) en una sola solución funcional, logrando una interacción con latencia mínima y alta estabilidad gracias al manejo correcto de los eventos y la sincronización de la red.

---
### Colaboración y Pruebas de Campo: Llevando el Proyecto al Límite

Tras la integración de nuestras dos compañeras al grupo, decidimos realizar una jornada de trabajo intensivo en la Facultad de Derecho para asegurar que todo el equipo dominara el sistema. La sesión se dividió en dos etapas: Traspaso de Conocimiento y Pruebas de Alcance Real.

### 1. Mentoría y Armado de Hardware

Para nivelar los conocimientos técnicos, iniciamos con un taller práctico liderado por los integrantes Braulio y Luisa:

* *Circuito Receptáculo:* Luisa entregó LEDs y resistencias a las nuevas integrantes, explicándoles paso a paso cómo identificar la polaridad del componente y por qué es vital la resistencia de 220Ω para proteger el Arduino.
<img width="330" height="350" alt="image" src="https://github.com/user-attachments/assets/7d7d0fd9-de85-407a-8b4c-27254682a7b0" />
<img width="330" height="350" alt="image" src="https://github.com/user-attachments/assets/0f535e20-4df7-4f12-9076-ad1b21625587" />
<img width="330" height="350" alt="image" src="https://github.com/user-attachments/assets/324b9751-9669-4ad2-be80-f6f05fbbd75f" />


<div align="center"> <video src="https://github.com/user-attachments/assets/1835f022-0868-4183-b9ba-8e42ec270b21" width="315" autoplay loop muted playsinline></video> </div>


* *Circuito Emisor:* Braulio lideró la explicación de la Raspberry Pi Pico 2 W, mostrando cómo conectar el botón de 4 pines y recordando la importancia de la configuración Pull-UP interna para evitar el ruido eléctrico.

<img width="579" height="662" alt="image" src="https://github.com/user-attachments/assets/d5bd1241-2c3d-4a62-9dd8-3319063ed601" />

---
### 2. Pruebas de Distancia y Obstáculos (Stress Test)

Una vez que los dos nodos estuvieron operativos, salimos a probar la estabilidad de la conexión inalámbrica bajo distintas condiciones:

* *Prueba de Obstáculos:* Colocamos las placas separadas por una pared de vidrio. A pesar del obstáculo físico, la señal se mantuvo estable gracias a que ambas compartían la misma red WiFi de 2.4GHz.

<div align="center"> <video src="https://github.com/user-attachments/assets/f28bb838-175e-4d0b-ac70-f5c7eef1f5f3" width="315" autoplay loop muted playsinline></video> </div>
  
* *El desafío de la red móvil:* Al intentar alejarnos más, la conexión se perdió. Identificamos que el problema era la fuente del WiFi: cuando el emisor de la señal (Hotspot móvil) se alejaba demasiado de una de las placas, esta quedaba fuera de la red. Solución: Tuvimos que independizar la red y asegurar que ambos nodos tuvieran cobertura constante, entendiendo que el IoT depende críticamente de la infraestructura de red.
  
* *Prueba de 15 Metros:* Con una red estable, logramos una respuesta instantánea a 15 metros de distancia lineal.

<div align="center"> <video src="https://github.com/user-attachments/assets/8f455e6f-69a0-4889-9f7d-e224874dedad" width="315" autoplay loop muted playsinline></video> </div>

* *Prueba de Altura (Piso 3 vs Piso 1):* La prueba definitiva fue vertical. Ubicamos el Arduino (receptor) en el tercer piso y la Raspberry (emisor) en el primer piso. Al presionar el botón desde abajo, el LED en el tercer piso encendió sin retraso perceptible.

<div align="center"> <video src="https://github.com/user-attachments/assets/65c58277-9847-4ca5-b7d3-566cec492208" width="315" autoplay loop muted playsinline></video> </div

Conclusión de la Jornada

Estas pruebas nos permitieron confirmar que nuestro sistema no solo funciona en la mesa del laboratorio, sino que es capaz de atravesar estructuras sólidas y salvar distancias considerables. Aprendimos que la limitante no es la potencia de las placas, sino la gestión y alcance de la red WiFi, un aprendizaje fundamental para nuestra formación en interacción inalámbrica.

---
### Animaciones del proyecto

### Bibliografía
- [Introducción a Raspberry Pi Pico 2 W](https://cursos.mcielectronics.cl/2025/08/12/introduccion-a-raspberry-pi-pico-2-y-pico-2-w/)
- [Pinout oficial Raspberry Pi Pico 2 W](https://datasheets.raspberrypi.com/picow/pico-2-w-datasheet.pdf)
- [CircuitPython: Beginner's Guide](https://circuitpython.org/)
- [Adafruit IO con CircuitPython](https://learn.adafruit.com/welcome-to-circuitpython/circuitpython-libraries)
- [Bibliotecas de Adafruit para CircuitPython](https://circuitpython.org/libraries)
- [Arduino y comunicación WiFi con Adafruit IO](https://learn.adafruit.com/adafruit-io-basics-analog-output)
- [Control de LED con Arduino](https://docs.arduino.cc/built-in-examples/basics/Blink/)
- [Configuración de baudios en Arduino IDE](https://docs.arduino.cc/software/ide-v2/tutorials/ide-v2-serial-monitor/)
- [Adafruit IO y MQTT](https://io.adafruit.com/api/docs/mqtt.html)
