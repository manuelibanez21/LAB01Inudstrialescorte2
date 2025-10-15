# 🌳 Topología en Árbol – Comunicación entre Equipos

## ⚙️ Descripción General

En esta práctica se implementó una **topología en árbol**, donde varios equipos fueron interconectados mediante un **switch configurado previamente** con los mismos parámetros utilizados en la **topología de estrella**.

En esta ocasión **no se utilizó un Arduino**, ya que el objetivo fue **comprobar la conectividad en red** entre los distintos equipos a través de **ping** y verificar la correcta **asignación automática de direcciones IP mediante DHCP**.

Como evidencia del montaje y pruebas realizadas se encuentran las imágenes denominadas:  
📸 **`arbol1.png`** hasta **`arbol13.png`**, las cuales muestran paso a paso el armado de la red y la ejecución de las pruebas de comunicación.

---

## 🧩 Estructura de la Topología

La topología en árbol se conformó a partir del **switch central**, al cual se conectaron varios equipos distribuidos en distintos niveles jerárquicos (rama principal y subramas).

- **Switch principal:** Configurado con VLAN1 y DHCP habilitado.  
- **Equipos finales:** Cada PC conectado a diferentes puertos del switch.  
- **Direcciones IP:** Asignadas automáticamente por DHCP dentro del rango 192.168.1.x.  

Esta estructura permitió una **comunicación eficiente y organizada**, similar a una red corporativa escalonada.

---

## 🧠 Flujo de Comunicación

1. El **switch principal** distribuye automáticamente las direcciones IP mediante **DHCP**.  
2. Cada **PC** obtiene su dirección IP y puede comunicarse con los demás equipos en la misma VLAN.  
3. Se realizan **pruebas de conectividad (ping)** entre todos los dispositivos conectados.  
4. Se verifica que **todas las ramas del árbol** (nodos secundarios y principales) puedan comunicarse sin pérdida de paquetes.

---

## 🖧 Configuración del Switch

Se utilizó **la misma configuración aplicada en la topología de estrella**, conservando:

- **VLAN1** como red principal.  
- **Activación de todos los puertos.**  
- **DHCP habilitado** para asignar IPs automáticamente.  
- **Modo de operación estático**, sin cambios en las VLANs adicionales.  

Esto permitió una transición directa de la topología de estrella a la de árbol, manteniendo compatibilidad total con las direcciones IP y el plan de red.

---

## 🔍 Pruebas Realizadas

- Todos los equipos obtuvieron **direcciones IP dinámicas** dentro del rango DHCP (192.168.1.x).  
- Las pruebas de **ping** entre cada nodo fueron **exitosas en todos los casos**.  
- No se detectaron conflictos de direcciones ni pérdida de paquetes.  
- La **latencia promedio fue mínima**, demostrando una correcta configuración de red.

---

## 📊 Resultados

| Prueba                | Acción Realizada                | Resultado         |
|------------------------|---------------------------------|-------------------|
| Asignación DHCP        | IP automática para cada PC      | Correcto ✅        |
| Comunicación VLAN1     | Ping entre todos los equipos    | Estable ✅         |
| Conectividad total     | Nodo raíz ↔ Nodos secundarios   | Confirmada ✅      |
| Estructura jerárquica  | Configuración tipo árbol        | Correcta ✅        |

---

## 📸 Evidencias

Se registraron imágenes del proceso de configuración, conexión y prueba de red:  
**`arbol1.png` → `arbol13.png`**  
Estas muestran la topología física y las respuestas exitosas de los comandos **ping**, confirmando la comunicación efectiva entre los equipos.

---

## ✅ Conclusión

La **topología en árbol** permitió verificar la escalabilidad de la red manteniendo la misma configuración del switch utilizada en la topología de estrella.  
Todos los equipos se comunicaron correctamente, confirmando que el **DHCP y la VLAN1** funcionaron de forma estable y sin interferencias.
