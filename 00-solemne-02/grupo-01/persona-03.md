# investigaciones individuales

Angel David Sabogal Hernandez / angel-udp

En el traspaso de información es importante mirar cada detalle que puede hacer que no funcione y en este caso puede pasasr que a pesar de tener bien el código que se sube a la nube si los jumpers no están bien conectados a cada uno de los pines que deben ir, podemos recaer en el fallo de pensar que es alguna parte del codigo o cualquier otra cosa, lo anoto como fallo de proceso porque a pesar de que llevamos un tiempo esto pueed seguir pasando hasta cuando se tiene bastante experiencia

Para la Pantalla OLED 128x84 px se debe implementar la app Putty que es la que hace que el codigo del dibujo digital y la fugura que muestra en que parte de los 180° se encuentra el movimiento de la perilla (parte izquierda)

## Sensor

El sensor está conectado en la misma protoboard que en la que esta la pastalla oled con la Raspberry Pi Pico 2 W, en este caso es el potenciometro de 10k el cual se activa girando la perilla y posteriormente pulsando el botón que es el que corrobora los cabios realizados desde su ultima posición

Este esta conectado mediante un cable USB a tipo micro USB a una computadora portatil diferente de a la que está conectado el actuador

## Actuador

Los servomotores son dispositivos rotativos diseñados para ofrecer un control extremadamente preciso de la posición, la velocidad y el torque. A diferencia de los motores convencionales que giran continuamente, los servos se mueven a posiciones específicas y se corrigen en tiempo real ante cualquier alteración.

Entonces el Servomotor utilizado en este caso es nuestro actuador el cual se irá moviendo a medida que vayamos ajustando los grados desde nuestro sensor que serian 180° como maximo que se pueda mover (almenos en ls prubas que estuvimos haciendo con el código en clases)

Este va conectado a los pines del arduino en otra protoboard diferente a la del sensor, el arduino está conectado mediante clabe USB a tipo C directamente a la computadora portatil

## Bibliografía

- Adafruit Learning System. (s.f.). Monochrome OLED breakouts. Adafruit Industries.
- Arduino Documentation. (s.f.). Analog input. Arduino.
- Raspberry Pi Documentation. (s.f.). Pico series. Raspberry Pi Ltd.
- Raspberry Pi Projects. (s.f.). Physical computing. Raspberry Pi Foundation.
