# 🧩 Topología Parcial – Comunicación Serial con Nodo Aislado

## ⚙️ Descripción General

En esta práctica se desarrolló una **topología parcial** basada en el mismo principio de comunicación en anillo, pero **rompiendo el lazo cerrado** para simular una **conexión incompleta o parcialmente conectada**.  

Se utilizaron **tres computadores con tres Arduinos**, empleando el mismo script de comunicación serie, pero **aislando el primer Arduino** para evitar el bucle continuo del mensaje.  
El objetivo fue observar el **comportamiento de la red ante la pérdida o desconexión de un nodo**, así como los efectos de una **topología no cerrada** sobre la transmisión de datos.

📸 **Evidencias:**  
Imágenes `parcial1.png` → `parcial7.png`  
🎥 **Video**: muestra el funcionamiento, la caída del enlace y el comportamiento erróneo al romper la conexión.

---

## 🧩 Estructura de la Topología

En esta configuración, se mantuvo la conexión entre los **Arduino 2 y Arduino 3**, pero **Arduino 1 quedó aislado** para simular una ruptura en la cadena de comunicación:

PC1 ↔ Arduino1 Arduino2 ↔ Arduino3 ↔ PC3
✖ ↑
│ │
(Aislado) Comunicación activa

arduino
Copiar código

De esta forma, los mensajes **no circulan en bucle**, sino que **viajan de forma unidireccional** entre los nodos conectados.

---

## 💻 Código Utilizado en los Arduinos

Se utilizó el mismo código base de la topología en anillo, sin modificaciones funcionales:

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
🧠 Flujo de Comunicación
El PC1 envía mensajes a su Arduino, pero al estar aislado, el mensaje no se propaga al resto de la red.

Arduino2 y Arduino3 mantienen comunicación directa entre ellos, reenviando los mensajes correctamente.

Al desconectar alguno de los pines RX o TX, la comunicación se interrumpe, generando mensajes incompletos o erróneos.

El video demuestra cómo la pérdida de conexión entre nodos afecta la transmisión y cómo el sistema deja de recibir mensajes válidos.

🔍 Pruebas Realizadas
Envío de mensajes desde cada PC para observar el alcance de la comunicación.

Aislamiento físico del Arduino1, comprobando la pérdida del bucle.

Desconexión de líneas TX o RX para analizar la caída parcial de la red.

Observación de mensajes corruptos o incompletos al interrumpir la comunicación.

📊 Resultados
Prueba	Descripción	Resultado
Aislamiento del Arduino1	Nodo fuera de la red, sin reenvío de datos	✅ Correcto
Comunicación Arduino2 ↔ Arduino3	Transmisión directa y estable	✅ Correcto
Desconexión RX/TX entre nodos	Interrupción parcial del flujo	⚠️ Observado
Mensajes corruptos o incompletos	Ocasionados por caída de enlace	⚠️ Verificado
