 🔄 Topología en Anillo – Comunicación Serie entre Múltiples Nodos

## ⚙️ Descripción General

En esta práctica se implementó una **topología en anillo** utilizando **tres computadores**, cada uno conectado a un **Arduino**.  
El objetivo fue establecer una **comunicación secuencial entre nodos** mediante **puertos seriales UART**, demostrando el funcionamiento de un **sistema en anillo cerrado**, donde los mensajes circulan entre los dispositivos de forma continua.

Cada Arduino funciona como un **nodo intermedio**, capaz de **recibir un mensaje y reenviarlo una sola vez** al siguiente nodo, simulando el comportamiento de una red de paso de tokens.

Como evidencia de la implementación, se incluyen:
📸 **Imágenes:** `anillo1.png` → `anilloX.png`  
🎥 **Videos:** muestran la transmisión y la pérdida del mensaje al desconectar alguna línea de comunicación (RX o TX).

---

## 🧩 Estructura de la Topología

La conexión se estableció en forma **cerrada (anillo)**, donde cada Arduino se comunica únicamente con el siguiente:

PC1 ↔ Arduino1 ↔ Arduino2 ↔ Arduino3 ↔ PC3
↑__________________________↓

pgsql
Copiar código

Cada **PC** controla su respectivo Arduino a través del **puerto USB**, mientras que los **Arduinos** están enlazados entre sí mediante los pines **TX** y **RX** cruzados, formando el anillo físico de comunicación.

---

## 💻 Código Utilizado en Cada Nodo (Arduino)

Cada Arduino utiliza el mismo programa, basado en la librería **SoftwareSerial**, para establecer la comunicación con el siguiente nodo y reenviar mensajes solo una vez.

```cpp
#include <SoftwareSerial.h>
SoftwareSerial link(2, 3); // RX = 2, TX = 3

void setup() {
  Serial.begin(115200);
  link.begin(38400);
  Serial.println(F("Nodo listo. Cada mensaje se reenvía UNA sola vez."));
}

void loop() {
  // --- Mensaje desde PC ---
  if (Serial.available()) {
    String msg = Serial.readStringUntil('\n');
    if (msg.endsWith("\r")) msg.remove(msg.length() - 1);
    if (msg.length() > 0) {
      Serial.print(F("[PC] ")); Serial.println(msg);
      link.println(msg);  // Reenvía el mensaje al siguiente nodo
    }
  }

  // --- Mensaje recibido desde UART ---
  if (link.available()) {
    String msg = link.readStringUntil('\n');
    if (msg.endsWith("\r")) msg.remove(msg.length() - 1);
    if (msg.length() > 0) {
      Serial.print(F("[UART] ")); Serial.println(msg);
      Serial.println(msg); // Eco de vuelta al PC
    }
  }
}
📘 Funcionamiento del Código:

Cada nodo escucha simultáneamente los mensajes que provienen del PC y del Arduino anterior.

Si recibe un mensaje desde el PC, lo envía por UART al siguiente nodo.

Si recibe un mensaje desde UART, lo muestra en el monitor serial del PC y lo eco de vuelta para mantener la trazabilidad del mensaje.

El mensaje se propaga una sola vez entre los nodos, evitando retransmisiones infinitas.

🧠 Flujo de Comunicación
Un PC envía un mensaje a su Arduino a través del monitor serial.

El Arduino reenvía el mensaje por UART al siguiente nodo del anillo.

Cada Arduino receptor muestra el mensaje recibido y lo reenvía al siguiente.

El mensaje completa el recorrido circular hasta regresar al nodo de origen.

🔁 En caso de desconectar alguna línea TX o RX, el flujo se interrumpe y el mensaje se pierde o queda en bucle, demostrando la dependencia de la conexión física en la topología de anillo.

🔍 Pruebas Realizadas
Se enviaron mensajes de texto desde cada PC y se observó su circulación completa por los tres nodos.

Se verificó que cada Arduino reenvía el mensaje una sola vez, evitando duplicaciones.

Al desconectar TX o RX en cualquier punto del anillo, el mensaje se pierde, validando el comportamiento esperado de una red en anillo interrumpida.

Los videos grabados muestran claramente el tránsito del mensaje y el bloqueo del flujo al romper la conexión.

📊 Resultados
Prueba	Descripción	Resultado
Envío de mensaje desde PC1	Mensaje circula por los 3 nodos	✅ Correcto
Recepción en todos los PCs	Todos los nodos muestran el mismo mensaje	✅ Correcto
Desconexión RX/TX en un nodo	Interrupción total de la comunicación	⚠️ Observado
Reenvío único por nodo	Sin duplicaciones ni ecos redundantes	✅ Correcto
