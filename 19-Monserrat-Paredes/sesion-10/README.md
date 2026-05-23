# sesion-10

lunes 18 mayo 2026

solemne 2

## Apuntes en clases

- Sensores: Dispositivo diseñado para detectar cambios en el entorno.
- Actuadores: Dispositivo que recibe una orden o señal.

Lo que queremos realizar en la solemne 2 es que desde la Raspberry pi envíe datos de un botón on/off  hacia el Arduino
y que este encienda una luz o emita algún sonido.

### Pseudocódigo

|Raspberry Pi Pico 2 W|Adafruit IO|Arduino UNO R4 wifi|
|--|--|--|
|Botón|MQTT|LED|
|ON/OFF|feed:estado|verde/rojo|

1. Presionar elbotón en la raspberry > alterna entre ON y OFF
2. La Raspberry publica "ON" "OFF" en el feed de Adafruit IO
3. El Arduino recibe el mensaje y enciende el LED correspondiente

#### Raspberry Pi Pico 2W

- Cada vez que presionas el botón, alterna entre ON y OFF (no es necesario mantenerlo)
- El LED integrado de la placa te muestra el estado actual (encendido=ON, apagado=OFF)
- Publica el texto "ON" u "OFF" en el feed estado de Adafruit IO
  
#### Arduino UNO R4 Wifi

- Recibe "ON" -> Enciende LED verde (D2), apaga el rojo
- Recibe "OFF" -> enciende LED rojo (D3), apaga el verde
- Al arrancar, ambos  LEDS parpadean 3 veces para confirmar que la conexión fue exitosa
  
#### Arduino UNO R4 Wifi

- Recibe "ON" -> Enciende LED verde (D2), apaga rojo
- Recibe "OFF" -> enciende LED rojo (D3), apaga verde
- Al arrancar, ambos LEDS parpadean 3 veces para confirmar que l aconexión fue existosa

NO OLVIDAR!

1. TU_NOMBRE_WIFI / TU_CLAVE_WIFI
2. TU_USUARIO_ADAFRUIT / TU_AIO_KEY
3. En el .ino, también reemplaza TU_USUARIO_ADAFRUIT en la línea del feed
4. Crear el feed llamado estado en tu cuenta de Adafruit IO antes de ejecutar

Después decidimos cambiarlo 


## Investigación

Lo que queremos realizar en la solemne 2 es que desde la Raspberry pi envíe datos mediante un potenciómetro hacía el Arduino y que este encienda una luz y mueva un servomotor. Los datos enviados se verán reflejados en el feed de Adafruit.

|Raspberry Pi Pico 2 W|Adafruit IO|Arduino UNO R4 wifi|
|---|---|---|
|Potenciómetro|MQTT|Led + servomotor|
|ángulo|Feed: estado|enciende led y mueve servo|

1. Girar el potenciómetro en la Raspberry -> va cambiandi el ángulo
2. La Raspberry publica e ángulo en el feed de Adafruit IO
3. El Arduino recibe el mensaje y mueve el servomotor. Al llegar a cierto ángulo se enciende el LED

### Raspberry Pi Pico 2 W

La placa **Raspberry Pi Pico 2 W** será la encargada de capturar los datos generados por un potenciómetro B500K conectado a una de sus entradas analógicas.

A medida que el usuario gira el potenciómetro, se producen variaciones de resistencia que la Raspberry interpreta como distintos valores analógicos. Estos valores son convertidos a datos digitales y posteriormente enviados de manera inalámbrica mediante conexión WiFi hacia la plataforma **Adafruit IO**, utilizando el protocolo de comunicación MQTT.

El propósito de esta etapa es transmitir y visualizar en tiempo real los cambios del potenciómetro dentro del feed denominado *“moluscos”*, permitiendo monitorear el comportamiento del sensor de forma remota a través de internet. Además, esta comunicación servirá como puente para controlar otros dispositivos conectados al sistema.

---

### Adafruit IO

La plataforma **Adafruit IO** funcionará como intermediario de comunicación entre ambas placas del proyecto.

Los datos enviados desde la Raspberry Pi Pico 2 W serán publicados continuamente en el feed *“moluscos”*, donde quedarán almacenados y disponibles en tiempo real. Posteriormente, estos valores podrán ser leídos y procesados por el Arduino UNO R4 WiFi para ejecutar distintas acciones físicas dentro del sistema.

Gracias a esta plataforma, es posible establecer una comunicación remota entre dispositivos IoT, facilitando la transmisión y sincronización de datos desde cualquier red con acceso a internet.

---

### Arduino IDE y Arduino UNO R4 WiFi

El **Arduino UNO R4 WiFi** será programado mediante el software **Arduino IDE** y se conectará a Adafruit IO para recibir los datos publicados en el feed *“moluscos”*.

Una vez recibidos los valores provenientes del potenciómetro, el Arduino interpretará esta información para controlar el movimiento de un servomotor SG90. Dependiendo de los datos enviados por la Raspberry Pi Pico 2 W, el servomotor ajustará su ángulo de posición de manera dinámica.

Además, cuando el servo alcance un ángulo específico previamente definido dentro del código, el sistema activará un LED amarillo como indicador visual, señalando que se alcanzó la posición programada. Esto permitirá generar una respuesta física inmediata a partir de los datos transmitidos por internet.

---

### Importante

Antes de cargar el código en las placas, es necesario reemplazar correctamente los siguientes datos de configuración:

* `TU_NOMBRE_WIFI`
* `TU_CLAVE_WIFI`
* `TU_USUARIO_ADAFRUIT`
* `TU_AIO_KEY`

Además, dentro del archivo `.ino`, se debe reemplazar el texto `TU_USUARIO_ADAFRUIT` en la línea correspondiente al feed para asegurar la correcta conexión con la cuenta de Adafruit IO.


## Investigación del sensor: Potenciómetro B500K

1. Investigación Sensor: Potenciómetro B500K (500K Ohm)
¿Qué es un potenciómetro?

El potenciómetro es un componente electrónico utilizado para variar manualmente la resistencia dentro de un circuito. Funciona como un resistor variable que permite modificar valores eléctricos, principalmente voltaje o corriente, dependiendo de la posición de su perilla o eje giratorio.

El modelo B500K posee una resistencia máxima de 500 kilo-ohmios y una curva lineal (“B”), lo que significa que el cambio de resistencia ocurre de manera proporcional al movimiento realizado por el usuario.

El potenciómetro es ampliamente utilizado en proyectos interactivos, diseño de interfaces físicas, instrumentos musicales, sistemas de control y proyectos de arte electrónico, ya que permite traducir movimientos humanos en datos digitales fáciles de interpretar por microcontroladores como Arduino o Raspberry Pi.

Funcionamiento del sensor

El potenciómetro posee tres terminales:

Un terminal conectado a voltaje (VCC).
Un terminal conectado a tierra (GND).
Un terminal central que entrega el valor variable.

Cuando el usuario gira la perilla, cambia la resistencia interna del componente, generando distintos niveles de voltaje en la salida analógica. Estos cambios son leídos por una entrada analógica del microcontrolador y convertidos en valores digitales.

Por ejemplo:

Giro mínimo → valor cercano a 0.
Giro medio → valor intermedio.
Giro máximo → valor cercano al máximo permitido por la placa.

Esto permite utilizar el potenciómetro como interfaz de control para modificar parámetros como:

Intensidad lumínica.
Velocidad de motores.
Volumen.
Posición de servos.
Variables visuales o sonoras.
Filtrado de información

Uno de los aprendizajes importantes al trabajar con sensores analógicos es el filtrado de datos.

Aunque el potenciómetro entrega valores relativamente estables, pueden aparecer pequeñas fluctuaciones debido a:

Ruido eléctrico.
Variaciones de alimentación.
Movimiento inestable del usuario.
Sensibilidad de lectura analógica.

Para evitar lecturas erráticas, es común aplicar técnicas de filtrado, como:

Promedio de lecturas

Consiste en tomar varias muestras consecutivas y calcular un promedio para suavizar las variaciones.

Rango mínimo de cambio

Permite ignorar pequeños cambios irrelevantes entre lecturas.

Delay o tiempo de estabilización

Reduce la velocidad de actualización para evitar lecturas excesivamente sensibles.

Estas técnicas ayudan a obtener datos más estables y confiables, especialmente en proyectos interactivos o visualizaciones en tiempo real.

Visualización de datos

Los datos del potenciómetro pueden visualizarse de distintas maneras:

Monitor Serial de Arduino IDE.
Plataformas IoT como Adafruit IO.
Gráficos en tiempo real.
Interfaces audiovisuales en TouchDesigner.
Sistemas interactivos de iluminación o sonido.

La visualización de datos permite observar cómo las acciones físicas del usuario afectan el sistema en tiempo real, facilitando el análisis, la interacción y el entendimiento del comportamiento del sensor.

En proyectos de diseño interactivo, el potenciómetro suele utilizarse como una interfaz tangible que conecta el movimiento físico con respuestas digitales.

Problemas comunes
Lecturas inestables

Pueden producirse por conexiones deficientes o ruido eléctrico.

Saltos bruscos en los valores

Suceden cuando el potenciómetro está desgastado o tiene suciedad interna.

Mala conexión de tierra (GND)

Puede provocar datos incorrectos o fluctuaciones extremas.

Rango de lectura incorrecto

Ocurre cuando la alimentación o la programación no coinciden con las capacidades del microcontrolador.

Desgaste mecánico

El uso constante puede deteriorar la pista resistiva interna del potenciómetro.

Proyecto o referente artístico
Rafael Lozano-Hemmer

El trabajo de Rafael Lozano-Hemmer combina sensores, interacción humana y visualización de datos en instalaciones electrónicas de gran escala.

Muchas de sus obras utilizan interfaces físicas y sistemas de captura de datos para transformar acciones humanas en experiencias audiovisuales. Aunque no trabaja exclusivamente con potenciómetros, utiliza constantemente sistemas de control analógico y sensores interactivos para modificar luz, sonido y movimiento en tiempo real.

Sus proyectos exploran la relación entre cuerpo, tecnología e interacción, convirtiéndose en un referente importante para el diseño de experiencias interactivas y arte electrónico contemporáneo.

2. Investigación Actuador: Servomotor SG90
¿Qué es un actuador?

Un actuador es un componente capaz de transformar energía eléctrica en movimiento físico o acciones mecánicas.

A diferencia de los sensores, que capturan información del entorno, los actuadores responden a datos o instrucciones generando una acción concreta.

Dentro de los actuadores más utilizados en proyectos interactivos se encuentran:

Motores DC.
Servomotores.
LEDs.
Relés.
Buzzers.
Solenoides.
¿Qué es el servomotor SG90?

El SG90 Micro Servo es un micro servomotor utilizado para controlar posiciones angulares con precisión.

Puede girar normalmente entre 0° y 180°, dependiendo de la señal enviada desde un microcontrolador.

Es ampliamente utilizado en:

Robótica.
Automatización.
Instalaciones interactivas.
Proyectos de diseño físico.
Sistemas cinéticos.
Funcionamiento del servomotor

El servo posee tres conexiones:

Alimentación (5V).
Tierra (GND).
Señal PWM.

El microcontrolador envía pulsos PWM (modulación por ancho de pulso), y el servo interpreta estos pulsos como posiciones angulares específicas.

Por ejemplo:

0° → posición inicial.
90° → posición media.
180° → posición máxima.

Esto permite controlar movimientos precisos de manera simple y eficiente.

Filtrado y control de movimiento

Al trabajar con actuadores, también es importante controlar la estabilidad de los datos recibidos.

Si el sensor entrega información muy variable, el servo puede:

Vibrar constantemente.
Moverse de forma brusca.
Generar ruido mecánico.
Sobrecalentarse.

Para evitar esto, se utilizan estrategias como:

Suavizado de movimiento

Realizar transiciones graduales entre posiciones.

Limitación de rango

Evitar movimientos extremos innecesarios.

Filtrado de datos del sensor

Reducir fluctuaciones antes de enviar información al servo.

Tiempo de actualización controlado

Evita movimientos excesivamente rápidos.

Visualización de datos y comportamiento

El movimiento del servo puede utilizarse como una forma física de visualización de datos.

Por ejemplo:

Representar intensidad sonora.
Mostrar variaciones lumínicas.
Indicar proximidad.
Traducir información digital en movimiento tangible.

Esto convierte al servomotor en un elemento importante dentro de proyectos interactivos y experiencias físicas de datos.

Problemas comunes
Vibración constante

Generalmente causada por ruido en la señal o alimentación insuficiente.

Falta de fuerza

Ocurre cuando el servo intenta mover demasiado peso.

Reinicios del microcontrolador

Suceden cuando el servo consume más corriente de la disponible.

Movimiento impreciso

Puede deberse a errores en la señal PWM o interferencias eléctricas.

Sobrecalentamiento

Provocado por movimientos forzados o uso continuo.
