# solemne-02

## Integrantes
- Braulio Figueroa / github: [brauliofigueroa2001](https://github.com/brauliofigueroa2001)
- Luisa Toro / github: [Luisaatoro9](https://github.com/Luisaatoro9)
- Marlén Soto / github: [marlensoto-lab](https://github.com/marlensoto-lab)
- Marcela Zúñiga / github: [marcezm](https://github.com/marcezm)


## Descripción textual del proyecto Trabajo en clases 
Inicialmente, nuestro proyecto consistía en desarrollar un sistema IoT distribuido utilizando una Raspberry Pi, un Arduino UNO R4 WiFi, un sensor ultrasónico HC-SR04 y un micro servo motor SG90, conectados mediante la plataforma Adafruit IO utilizando el protocolo MQTT.

La Raspberry Pi tendría la función de controlar el sensor ultrasónico HC-SR04, medir la distancia de un objeto y enviar periódicamente los datos obtenidos hacia Adafruit IO a través de internet. Posteriormente, el Arduino UNO R4 WiFi consultaría la información almacenada en la plataforma y, según la distancia recibida, controlaría el movimiento del servo motor SG90.

El objetivo principal del proyecto era demostrar la comunicación inalámbrica entre distintos dispositivos mediante tecnologías IoT, integrando la adquisición de datos físicos, la transmisión en la nube y el control remoto de actuadores en tiempo real. Además, para evitar saturar el servicio gratuito de Adafruit IO, el sistema incorporaría intervalos de tiempo entre cada envío de datos.

## Proceso realizado en clases

Durante el desarrollo del proyecto comenzamos realizando el cableado de la Raspberry Pi junto con el sensor de distancia. Debido a que no teníamos experiencia previa trabajando con este tipo de sensores ni con la Raspberry Pi, fue necesario investigar profundamente el funcionamiento del hardware y sus conexiones, proceso que nos tomó aproximadamente una hora.

Posteriormente, trabajamos en la programación del sensor y del botón, pero surgieron diversas dificultades relacionadas con librerías necesarias para el funcionamiento del sistema y múltiples errores en el código. Intentamos resolver estos problemas durante otra hora adicional, investigando posibles soluciones y realizando distintas pruebas, pero no logramos que el sistema funcionara correctamente dentro del tiempo disponible.

Finalmente, debido a la falta de tiempo para continuar avanzando con nuestro proyecto inicial, tuvimos que incorporarnos al Grupo 10, integrado por Braulio Figuerio y Luisa Torres, con el fin de continuar el trabajo práctico de la clase.

## Materiales usados en clases 
| Material | Cantidad | Precio aproximado (CLP) |
|---|---:|---:|
| Raspberry Pi Pico 2W | 1 | $15.990 |
| Arduino UNO R4 WiFi | 1 | $34.990 |
| HC-SR04 Ultrasonic Sensor | 1 | $3.290 |
| SG90 Micro Servo Motor | 1 | $1.830 |
| Protoboard | 1 | $2.590 |
| Cables Dupont | 1 pack | $1.990 |
| Cable USB | 1 | $3.000 |
| Fuente de alimentación USB | 1 | $8.000 |

## Descripción textual del proyecto

## Sensor usado
### Botón pulsador de 4 pines

El sensor utilizado en esta etapa del proyecto fue un botón pulsador de 4 pines, empleado como entrada digital para activar o desactivar el envío de datos hacia Adafruit IO.

Su función principal dentro del sistema es actuar como una “puerta de control”, permitiendo decidir cuándo la Raspberry Pi Pico 2W puede enviar información a la nube. De esta manera, se evita la saturación del servidor gratuito de Adafruit IO y se optimiza la comunicación entre los dispositivos IoT.

## Actuador usado
### Luz LED

El actuador utilizado en el proyecto fue una luz LED, empleada para representar visualmente la recepción de datos enviados desde la Raspberry Pi Pico 2W hacia el Arduino UNO R4 WiFi mediante Adafruit IO.

Su función principal dentro del sistema es encenderse o apagarse dependiendo del estado del botón conectado a la Raspberry Pi Pico 2W, permitiendo demostrar la comunicación inalámbrica y el control remoto de actuadores en tiempo real mediante tecnologías IoT.
## Código usado para enviar
```
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

        # presionado -> envía 1
        if estado_anterior and not estado_actual:
            print("Botón presionado")
            mqtt.publish(FEED_BOTON, "1")
            print("Enviado: 1")
            time.sleep(0.25)  # anti-rebote

        # soltado -> envía 0
        if not estado_anterior and estado_actual:
            print("Botón soltado")
            mqtt.publish(FEED_BOTON, "0")
            print("Enviado: 0")
            time.sleep(0.25)  # anti-rebote

        estado_anterior = estado_actual

    except Exception as e:
        print("Error, reconectando:", e)
        try:
            mqtt.reconnect()
        except:
            pass

    time.sleep(0.02)
```
### Proceso
Durante las primeras pruebas realizamos la ejecución del código inicial, pero el sistema no funcionó correctamente, ya que la luz LED no lograba encenderse. Debido a esto, fue necesario revisar y modificar el código para identificar el problema.

Con ayuda de nuestro compañero, nos dimos cuenta de que nuestra Raspberry Pi Pico 2W no contaba con algunas librerías necesarias para el correcto funcionamiento del programa, por lo que procedimos a instalarlas y configurar nuevamente el entorno de trabajo.

Además, observamos que la visualización del proyecto en Visual Studio Code aparecía con el ícono de Adobe Illustrator, lo que inicialmente nos generó confusión respecto al tipo de archivo y su configuración dentro del programa. Luego de revisar esto, continuamos realizando ajustes hasta lograr avanzar correctamente con el desarrollo del proyecto.
![VisualStudioCode](imagenes/Visualizacion.jpg)
## Código usado para recibir 
```
/*************************************************************
   PROYECTO: RECEPTOR ARDUINO (ESPEJO DE BOTÓN)
*************************************************************/
#include "AdafruitIO_WiFi.h"

// ==========================================
// CREDENCIALES
// ==========================================
#define IO_USERNAME  "udpmontoyamoraga"
#define IO_KEY       "clavecredencial"
#define WIFI_SSID    "marce"
#define WIFI_PASS    "marce1234"

// ==========================================
// CONFIGURACIÓN
// ==========================================

// Instancia Adafruit IO
AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);

// Pin del LED
const int ledPin = 13;

// Feed (debe ser el mismo que usa la Raspberry)
AdafruitIO_Feed *botonFeed = io.feed("boton-prueba-grupo10");

// ==========================================
// SETUP
// ==========================================
void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(115200);

  Serial.print("Conectando a Adafruit IO...");
  io.connect();

  // Función que reaccionará a los datos
  botonFeed->onMessage(handleMessage);

  // Esperar conexión
  while(io.status() < AIO_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.println("¡Arduino Conectado!");
}

// ==========================================
// LOOP
// ==========================================
void loop() {
  // Mantener conexión activa
  io.run();
}

// ==========================================
// FUNCIÓN QUE RECIBE DATOS
// ==========================================
void handleMessage(AdafruitIO_Data *data) {
  // Convertir dato recibido a entero
  int comando = data->toInt();

  // Si recibe 1 -> prender LED
  if (comando == 1) {
    digitalWrite(ledPin, HIGH);
    Serial.println("LED ON");
  }
  // Si recibe 0 -> apagar LED
  else {
    digitalWrite(ledPin, LOW);
    Serial.println("LED OFF");
  }
}
```
### Proceso
Al inicio de la sesión, realizamos una prueba utilizando un código desarrollado previamente por nuestra compañera, el cual ya había sido verificado y funcionaba correctamente. Posteriormente, decidimos desarrollar el proceso por nuestra cuenta, lo que derivó en una serie de errores, principalmente relacionados con el cableado.

El primer inconveniente fue la conexión de alimentación: el cable estaba conectado a 5V, cuando lo correcto era utilizar 13V, ya que la conexión inicial solo permitía verificar el funcionamiento del LED, pero no era la adecuada para el comportamiento esperado del sistema.

Una vez corregido ese punto, nos encontramos con un segundo problema: el LED no lograba apagarse correctamente. Para resolverlo, desarrollamos dos códigos adicionales, modificando distintas secciones de la programación y realizando múltiples pruebas. Sin embargo, ninguna de las modificaciones solucionó el inconveniente.
![Arduino](imagenes/error_prendido.jpg)

Finalmente, determinamos que el problema no estaba en el código, sino en la configuración interna de la Raspberry Pi Pico 2W. El dispositivo había sido modificado previamente y solo mantenía activa la señal de encendido del LED (valor `1`), mientras que la señal de apagado (valor `0`) no funcionaba correctamente. Al identificar este origen, volvimos al código inicial y pudimos continuar con el desarrollo del proyecto.

Como parte adicional de la práctica, quisimos comprobar si el sistema funcionaba a larga distancia. Para ello, fue necesario conectarnos a una red distinta, ya que al alejarnos del punto de acceso original la señal se debilitaba al punto de desconectarse. Al cambiar de red logramos mantener una conexión estable y verificar que el sistema respondía correctamente incluso a mayor distancia.

![Arduino](imagenes/arduino_conectado.jpg)
<img width="1825" height="865" alt="arduino_conectado" src="https://github.com/user-attachments/assets/7c703236-4e96-40bb-b194-9d0e745d945c" />

![Arduino](imagenes/prueba1.jpg)
## Imágenes del proyecto
Pruebas realizas de larga distancia

## Animaciones del proyecto

## Bibliografía
