# solemne-02

## Integrantes

- Martina Alegria / AlegriaColoma
- Antonella Lavalle / antolavalle
- Catalina Salinas / catasal

## Descripción textual del proyecto

* El proyecto consiste en desarrollar un sistema de comunicación entre una Raspberry Pi y un Arduino utilizando la plataforma Adafruit IO como intermediaria para el envío y recepción de datos en tiempo real. Para esto, se utilizará un potenciómetro conectado a la Raspberry Pi, el cual permitirá obtener valores analógicos según la posición del control giratorio.

* La Raspberry Pi leerá continuamente los datos entregados por el potenciómetro y los enviará a Adafruit IO mediante conexión a internet y el protocolo MQTT o HTTP. Estos datos quedarán almacenados y disponibles en la nube, permitiendo la comunicación entre distintos dispositivos conectados.

* Posteriormente, el Arduino se conectará a Adafruit IO para recibir los valores enviados desde la Raspberry Pi. Dependiendo del valor entregado por el potenciómetro, el Arduino podrá ejecutar distintas acciones, como controlar la intensidad de un LED, mover un servomotor o activar otros componentes electrónicos.

<img width="880" height="463" alt="inalambrico" src="https://github.com/user-attachments/assets/881f277e-f0f4-46e5-ac69-5ec51a5124f7" />

para optener el ip del arduino tuvimo que utilizar este codigo

#include <WiFiS3.h>

const char* ssid     = "TU_RED_WIFI";      // ← cambiá esto
const char* password = "TU_CONTRASEÑA";    // ← cambiá esto

void setup() {
  Serial.begin(9600);
  WiFi.begin(ssid, password);

  Serial.print("Conectando");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nIP del Arduino:");
  Serial.println(WiFi.localIP());
}

void loop() {
  // no hace nada más
}

* Subís el sketch al Arduino (botón de la flecha →)
* Abrís el Monitor Serie (Ctrl+Shift+M) y esperás unos segundos
* Ves la IP, por ejemplo: 192.168.1.105 → la anotás
* Después subís el código final del proyecto (el que escucha el puerto UDP) y ya no necesitás este sketch más


## Materiales usados

## Sensor usado

- Potenciometro
 
## Actuador usado

- LEDS
  
## Código usado para enviar

## Código usado para recibir

## Imágenes del proyecto

## Animaciones del proyecto

## Bibliografía
