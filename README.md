# Practica_11
# Integrantes:

• Luis Fernando Duarte Reséndiz

• Mauricio Alberto Gómez Arroyo

• Diego Brandon Guzmán Sierra

• Bryan Hiadim Vera Hernández

# Introducción

En el ámbito de la automatización industrial, los robots Epson destacan por su precisión y flexibilidad en la ejecución de tareas. Sin embargo, en ciertos casos, es necesario complementarlos con soluciones personalizadas que permitan un control remoto intuitivo y adaptable a las necesidades específicas del usuario. Aquí es donde el Arduino se convierte en una herramienta poderosa y accesible.

Arduino, con su capacidad para establecer comunicación serial (mediante protocolos como RS-232 o TTL), permite enviar comandos al robot Epson de manera eficiente, actuando como un "control remoto" que extiende las posibilidades de manejo del robot. Este enfoque es especialmente útil en aplicaciones como:

* Control básico de movimiento: Enviar comandos para desplazar el brazo del robot, posicionarlo en coordenadas específicas o ejecutar movimientos preprogramados.

# Instrucciones

La práctica consiste en desarrollar un sistema de comunicación serial entre un Arduino y un robot Epson C4, utilizando un potenciómetro como dispositivo de entrada para enviar comandos que controlen movimientos y acciones específicas del robot.

## Conexiones

### Establecer la comunicación entre el Arduino y el robot Epson:

Para esta practica se uso el adaptador RS-232 a TTL para realizar la conversión de niveles lógicos.

* Conectar el pin TX del Arduino al pin RX del robot Epson.

* Conectar el pin RX del Arduino al pin TX del robot Epson.

* Conectar GND del Arduino al GND del robot Epson para referencia común.

### Conectar el potenciómetro al Arduino:

* Conectar una pata del potenciómetro al pin A0 del Arduino.

* Conectar la segunda pata a GND.

* Conectar la tercera pata a la salida de 3.3V del Arduino.

Las conexiones  anteriores se verian de la siguiente forma como se observa en la imagen:

![WhatsApp Image 2024-12-05 at 09 47 57_cc532b5e](https://github.com/user-attachments/assets/08005f83-9700-4cc9-bd54-1c079bf0115d)

## Configuraciones del Arduino y del Robot Epson

### Carga del código Arduino:

Se utilizo el siguiente código para leer los valores del potenciómetro, convertirlos a comandos específicos (caracteres del 1 al 9) y enviarlos al robot Epson por comunicación serial:


```C
// Definir el pin donde está conectado el potenciómetro
const int potPin = A0; // Pin analógico A0

void setup() {
  // Inicializamos la comunicación serial para el monitor serie
  Serial.begin(9600);

  // Configurar el pin como entrada
  pinMode(potPin, INPUT);
}

void loop() {
  // Leer el valor analógico del potenciómetro
  int potValue = analogRead(potPin);

  // Convertir el valor leído (0-1023) a voltaje (0-3.3V)
  float voltage = potValue * (3.3 / 1023.0);

  // Dividir el rango del potenciómetro en 9 niveles y enviar los valores por Serial
  int valor = 670 / 9;
  
  if (potValue <= valor) {
    Serial.write(49); // Enviar el carácter '1' por Serial
    Serial.println(49); // Mostrar en el monitor serie
  }
  if (potValue <= valor * 2 && potValue > valor) {
    Serial.write(50); // Enviar el carácter '2' por Serial
    Serial.println(50);
  }
  if (potValue <= valor * 3 && potValue > valor * 2) {
    Serial.write(51); // Enviar el carácter '3' por Serial
    Serial.println(51);
  }
  if (potValue <= valor * 4 && potValue > valor * 3) {
    Serial.write(52); // Enviar el carácter '4' por Serial
    Serial.println(52);
  }
  if (potValue <= valor * 5 && potValue > valor * 4) {
    Serial.write(53); // Enviar el carácter '5' por Serial
    Serial.println(53);
  }
  if (potValue <= valor * 6 && potValue > valor * 5) {
    Serial.write(54); // Enviar el carácter '6' por Serial
    Serial.println(54);
  }
  if (potValue <= valor * 7 && potValue > valor * 6) {
    Serial.write(55); // Enviar el carácter '7' por Serial
    Serial.println(55);
  }
  if (potValue <= valor * 8 && potValue > valor * 7) {
    Serial.write(56); // Enviar el carácter '8' por Serial
    Serial.println(56);
  }
  if (potValue <= valor * 9 && potValue > valor * 8) {
    Serial.write(57); // Enviar el carácter '9' por Serial
    Serial.println(57);
  }

  // Esperar un poco antes de la próxima lectura
  delay(1000);
}


```

Para verificar la comunicación:
Abrir el monitor serial del IDE de Arduino para observar los valores que se envían.

### Carga del codigo del Robot Epson
El código en SPEL+ para el robot Epson implementa un sistema que permite recibir comandos a través de comunicación serial desde un Arduino y ejecutarlos para controlar movimientos y acciones. 
Primero, se inicializan las configuraciones del robot.

Luego, se llama a una función dedicada a manejar la comunicación serial. Esta función abre el puerto serial, verifica continuamente si hay datos en el buffer, y lee el dato recibido, que se almacena en la variable dato. 

En la parte principal del programa, un bucle infinito evalúa esta variable y, según el valor recibido, ejecuta una de varias acciones predefinidas, como mover el robot en direcciones específicas, encender o apagar salidas, o ajustar el factor de velocidad. 

Finalmente, el programa asegura que la comunicación serial esté cerrada al completar el proceso.

Programa principal en SPEL+:
```spel
Integer dato
Function main
Motor On
Home
Power High
SpeedS 1000
AccelS 10000, 10000
Speed 50
Accel 50, 50
Weight 0.1
Tool 1

Xqt serial
Do
	
	Select 1
		Case dato = 49
			Go izquierda
		Case dato = 50
			Go derecha
		Case dato = 51
			Go arriba
		Case dato = 52
			Go abajo
		Case dato = 53
			Off 2
		Case dato = 54
			On 2
		Case dato = 55
			SpeedFactor 10
		Case dato = 56
			SpeedFactor 50
		Case dato = 57
			SpeedFactor 100
		
	Send
	
Loop
Fend

Function serial
	Integer numChars
	OpenCom #1
	Wait 1
	Print "HOLA"
	Do
		Do While numChars = 0
			numChars = ChkCom(1)
		Loop
		ReadBin #1, dato
		Print dato
	Loop
	CloseCom #1
Fend
```

### Pruebas y validacion

* Iniciar el programa en el robot.

* Ejecutar el programa principal en el robot Epson.

* Girar el potenciómetro en el Arduino:

* Observar cómo el valor del potenciómetro se traduce en comandos (49 a 57) que controlan movimientos y acciones en el robot.

* Validar las acciones:

Confirmar que el robot realiza los movimientos esperados según la tabla de comandos:

49 ('1'): Mover a la izquierda.

50 ('2'): Mover a la derecha.

51 ('3'): Subir.

52 ('4'): Bajar.

Y así sucesivamente.

## Resultados


https://github.com/user-attachments/assets/9da57f0a-cb36-44b1-80ed-cae9ca5b8fa6


## Conclusiones
* Luis Fernando Duarte Reséndiz.

Es posible conectar y controlar un robot industrial, como el Epson C4, utilizando un microcontrolador Arduino mediante comunicación serial, demostrando que es viable integrar tecnología de bajo costo con sistemas industriales avanzados.

* Mauricio Alberto Gómez Arroyo.

Usar un potenciómetro como entrada de datos en el Arduino simplifica el control remoto del robot, dividiendo el rango de valores en comandos predefinidos para movimientos y ajustes de configuración, lo que facilita la interacción con el sistema.

* Diego Brandon Guzmán Sierra.

Tanto el programa en Arduino como el código en SPEL+ son lo suficientemente modulares para ser adaptados o ampliados, permitiendo agregar nuevas funcionalidades o cambiar los dispositivos de entrada según las necesidades del proyecto.

* Bryan Hiadim Vera Hernández.

Este sistema demuestra cómo herramientas de automatización simples pueden implementarse en entornos industriales, potenciando soluciones de control económico y personalizables para tareas específicas en áreas como manipulación de objetos o ajustes dinámicos de procesos.


## Referencias
De proyectos, A. y. D. (s/f). Manual del usuario. Epson.com. Recuperado de https://files.support.epson.com/far/docs/epson_rc_pl_70_users_guide_spanish_(v73r2).pdf







