# sesion-08 27.04.2026

## RaspBerry PI Pico

**Python:** Python es un lenguaje de programación de alto nivel, interpretado y de propósito general, reconocido como uno de los más populares y versátiles del mundo.

* Alguien creó micropython: Nadie lo usó
* Circuit python: muy weno instalemoslo.

> Descargar CircuitPython 10.2.0 en <https://circuitpython.org/board/raspberry_pi_pico/>

![raspberrypin](./imagenes/circuitpython.png)

`Importante descargar el correcto, no leo asi que primero descargué uno nada que ver, leer es importante`
  
### CircuitPython 10.2.0
Según Gemini es una versión estable y específica del lenguaje de programación CircuitPython, diseñada para funcionar en placas de microcontroladores, destacando su uso en la Circuit Playground Express. Es una implementación de código abierto de Python 3, mantenida por **Adafruit Industries**, enfocada en facilitar el aprendizaje de la programación en hardware

Una vez descargado, debemos inyectarlo a nuestra RaspBerry.

Luego descargar las bibliotecas:
* adafruit_minimqtt
* adafruit_connection_manager.mpy
* adafruit_ticks.mpy

### Pins de la Raspberry
![raspberrypin](./imagenes/raspberry.png)


* Conectamos un potenciometro de 200k Ohm

Patitas:
1. GND a través de un resistor 20k Ohm
2. ADC0 de raspi
3. 3V3

```
Vcc en arduino = 5V
Vcc en raspi = 3.3V
```
