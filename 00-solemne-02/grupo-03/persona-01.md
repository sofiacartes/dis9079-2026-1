# investigaciones individuales

nombre completo / github

## Sensor
sensor botón / pulsador

Para esta investigación trabajé con el botón o pulsador, que es uno de los sensores más simples y básicos dentro de la electrónica. Su función principal es detectar si está siendo presionado o no, por lo que solo entrega dos estados posibles: 0 o 1, apagado o encendido, abierto o cerrado. Por esto se considera un sensor digital, ya que no mide valores intermedios, sino que responde a una acción puntual.
A pesar de ser un componente muy simple, el botón es una de las formas más directas de interacción entre una persona y un sistema electrónico. Al presionarlo, el usuario entrega una instrucción clara al circuito, como encender una luz, activar un sonido, enviar un mensaje o iniciar una acción dentro de un programa. En ese sentido, el botón funciona como un puente entre una acción física humana y una respuesta digital. 
 
Funcionamiento

El botón funciona mediante dos contactos metálicos internos. Cuando no se presiona, estos contactos están separados y el circuito permanece abierto, por lo que la corriente no pasa. Al presionarlo, los contactos se juntan, el circuito se cierra y la corriente puede circular. Este cambio es leído por la placa, por ejemplo Arduino o Raspberry Pi, como una entrada digital.
Para que la lectura sea estable, normalmente se utiliza una resistencia pull-up o pull-down. Esta resistencia permite que el pin tenga un valor definido cuando el botón no está siendo presionado. Sin esta resistencia, el pin puede quedar “flotando”, es decir, sin un valor claro, lo que puede producir lecturas falsas o cambios inesperados.
En algunos casos, no es necesario agregar una resistencia física, ya que placas como Arduino permiten activar una resistencia interna desde el código usando INPUT_PULLUP. En este caso, el comportamiento puede parecer invertido: cuando el botón no está presionado puede leerse como HIGH, y cuando se presiona puede leerse como LOW.

Problemas que pueden aparecer

Uno de los problemas más comunes al trabajar con botones es el rebote. Esto pasa porque, al presionar el botón, los contactos internos no se juntan de manera completamente limpia, sino que generan pequeñas vibraciones durante unos milisegundos. Aunque para nosotros sea una sola presión, la placa puede leerlo como varias pulsaciones seguidas.
Para solucionar esto se usa el debounce, que básicamente sirve para evitar que el sistema registre muchas lecturas falsas. Se puede hacer desde el código dejando un pequeño tiempo de espera después de detectar una presión, o también desde el circuito, agregando componentes que ayuden a estabilizar la señal.

También pueden aparecer problemas más simples, pero igual de importantes, como cables mal conectados, pines equivocados o conexiones flojas en la protoboard. Por eso, antes de pensar que el código está malo, es importante revisar bien el montaje físico.
 
Visualización de datos
 
Como el botón solo entrega dos estados, su visualización es bastante fácil de entender. Por ejemplo, se puede prender un LED cada vez que el botón se presiona, o mostrar en pantalla si el valor está en 0 o 1. También se puede hacer un contador de pulsaciones, donde cada presión aumente un número.
Además, se pueden definir distintos mensajes según el orden de pulsación del botón. Por ejemplo, la primera vez que se presiona puede enviar el mensaje “botón 01”, la segunda vez “botón 02” y la tercera vez “botón 03”. De esta manera, una acción simple como presionar un botón puede transformarse en un sistema numérico o secuencial, donde cada pulsación tiene un significado distinto dentro del proyecto.
Otra forma de visualizarlo es como una señal que cambia en el tiempo: cuando el botón no está presionado se mantiene en un estado, y cuando se presiona cambia al otro. Esto permite ver de manera simple cómo una acción manual se transforma en un dato que el sistema puede interpretar.

## Actuador
 
 Un ejemplo de actuador que usamos con este sistema es el servo motor.

El botón funcionaba como entrada: cada vez que se presionaba, enviaba una señal o un mensaje según el orden de pulsación. Luego, esa información podía ser recibida por otro dispositivo para activar una respuesta física. En este caso, el servo motor podía moverse a distintas posiciones dependiendo del mensaje recibido.

Por ejemplo:

Pulsación del botón	Mensaje enviado	Acción del actuador
Primera pulsación	botón 01	El servo se mueve a 0°
Segunda pulsación	botón 02	El servo se mueve a 90°
Tercera pulsación	botón 03	El servo se mueve a 180°

Así, el botón no solo activa una acción, sino que permite generar una secuencia de instrucciones. El actuador, en este caso el servo, transforma esa información digital en un movimiento físico. Esto muestra cómo una interacción simple, como presionar un botón, puede producir una respuesta visible dentro del sistema. 

Referente: Karri Messenger

Como referente encontré Karri Messenger, un dispositivo de comunicación para niños que funciona principalmente con botones y mensajes de voz. Me llamó la atención porque no se plantea como un celular, sino como una forma más simple de comunicarse, sin tantas aplicaciones ni pantallas.

Lo relaciono con mi investigación porque el botón es la forma principal de interactuar con el objeto. Al presionarlo, el niño puede grabar un mensaje, enviarlo o cambiar de contacto. Entonces el botón no sirve solo para prender o apagar algo, sino que permite tomar decisiones dentro del sistema.

También me sirve para pensar cómo una misma acción puede tener distintos significados. Por ejemplo, una pulsación puede servir para seleccionar, otra para grabar y otra para enviar. Esto se parece a la idea de ordenar mensajes por número de pulsación, donde cada presión activa una respuesta distinta.

En este caso la salida no es un movimiento como el de un servo, sino una respuesta sonora y comunicacional: el dispositivo graba, reproduce o manda mensajes.



## Bibliografía
https://cursos.mcielectronics.cl/2024/10/22/explicando-el-modo-arduino-input_pullup-pinmode/ 
