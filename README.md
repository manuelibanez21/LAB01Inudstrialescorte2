# LAB01Inudstrialescorte2
# 🌐 Proyecto de Comunicación en Red con Switch y Control de LED mediante Socket

## 🧩 Descripción General

Este proyecto implementa una **topología en estrella** utilizando **tres computadores conectados a un switch**, simulando el comportamiento de una red distribuida tipo maestro–esclavo.  
En lugar de usar Raspberry Pi, se emplearon **computadores personales (PCs)** conectados entre sí mediante un **switch configurado manualmente**.

El objetivo principal fue **controlar un LED conectado a un Arduino físico**, a través de la **comunicación en red (VLAN1)** entre los equipos, usando **sockets TCP en Python**.

---

## 🖥️ Topología Implementada

- **PC1:** Configura el switch.  
- **PC2:** Actúa como **cliente maestro**, enviando mensajes por red.  
- **PC3:** Actúa como **servidor esclavo**, conectado a un Arduino que controla un LED físico.  
- **Switch:** Conecta los tres equipos a través de VLAN1, funcionando como el núcleo de la red.

📁 En el repositorio se incluyen las imágenes:
`estrella1.png` → `estrella7.png`, que muestran cada etapa del montaje físico y lógico.

---

## ⚙️ Configuración del Switch

Se utilizó un **switch Cisco** configurado desde la terminal.  
A continuación, se describen los comandos utilizados paso a paso:

```bash
Switch> enable
Switch# configure terminal
Switch(config)# vlan 1
Switch(config-vlan)# name VLAN1
Switch(config-vlan)# exit

Switch(config)# interface range fastEthernet 0/1 - 24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 1
Switch(config-if-range)# no shutdown
Switch(config-if-range)# exit

Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.1 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit

Switch(config)# ip default-gateway 192.168.1.1
Switch(config)# service dhcp
Switch(config)# ip dhcp pool VLAN1
Switch(dhcp-config)# network 192.168.1.0 255.255.255.0
Switch(dhcp-config)# default-router 192.168.1.1
Switch(dhcp-config)# exit
Switch(config)# end
Switch# write memory
##💡 Con esto, todos los puertos quedaron activos dentro de la VLAN1 y se asignaron direcciones IP automáticamente mediante DHCP.

🧠 Flujo Maestro–Esclavo

El cliente (PC2) ejecuta un script en Python que permite al usuario enviar comandos (on, off, etc.).

El servidor (PC3) recibe estos comandos por red y los reenvía al Arduino, el cual ejecuta la acción correspondiente (encender o apagar un LED).

El Arduino físico responde actuando sobre el LED, confirmando la correcta comunicación en red.
💻 Código del Maestro (PC2 – Cliente Python)
import socket

# Crear el socket
c = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Conectar con el servidor (PC3)
c.connect(('192.168.1.103', 5000))  # IP del servidor

print("Conectado al servidor. Escribe algo y presiona Enter. Ctrl+C para salir.")

while True:
    msg = input("> ")       # Captura el texto a enviar
    if not msg:             # Ignora mensajes vacíos
        continue
    c.send(msg.encode())    # Envía el mensaje al servidor

# Cierra el socket al finalizar
c.close()

🖥️ Código del Esclavo (PC3 – Servidor Python)

Este script escucha las conexiones entrantes y envía los comandos recibidos al Arduino mediante el puerto serial.

import socket
import serial

# Configura el puerto serial hacia el Arduino
arduino = serial.Serial('/dev/ttyUSB0', 9600)  # En Windows: COM3, por ejemplo

# Configura el socket del servidor
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('0.0.0.0', 5000))
server.listen(1)

print("Servidor listo, esperando conexión...")
conn, addr = server.accept()
print(f"Conectado con: {addr}")

while True:
    data = conn.recv(1024).decode().strip()
    if not data:
        continue
    print(f"Comando recibido: {data}")

    # Envía el comando al Arduino
    arduino.write(data.encode())

⚡ Código del Arduino (Esclavo Físico)

El Arduino recibe los mensajes del servidor Python y enciende o apaga el LED según el comando.

int led = 13;   // Pin del LED

void setup() {
  pinMode(led, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  if (Serial.available()) {
    String command = Serial.readStringUntil('\n');
    command.trim();

    if (command == "on") {
      digitalWrite(led, HIGH);
      Serial.println("LED encendido");
    } 
    else if (command == "off") {
      digitalWrite(led, LOW);
      Serial.println("LED apagado");
    }
  }
}

🔍 Pruebas Realizadas

Todos los PCs recibieron direcciones IP dentro del rango 192.168.1.x por DHCP.

La comunicación entre PC2 y PC3 mediante VLAN1 fue exitosa.

Al escribir on en el cliente Python, el LED físico en el Arduino se encendió.

Al enviar off, el LED se apagó.

Se probó la conexión y reconexión del socket sin pérdida de sincronía.

📊 Resultados
Prueba	Acción	Resultado
Conexión VLAN1	PC2 ↔ PC3	Comunicación estable
Comando “on”	LED encendido	✅ Correcto
Comando “off”	LED apagado	✅ Correcto
DHCP en Switch	IP asignadas correctamente	✅ Correcto
