# 🛡️ SentinelGuard: Sistema Biomecatrónico de Barrera Autónoma

Subsistema avanzado de sensado y actuación de precisión basado en la plataforma **Arduino**, optimizado para la automatización inteligente de contenedores o barreras perimetrales mediante telemetría ultrasónica y control cinemático de posicionamiento angular. La arquitectura integra un algoritmo de filtrado por promedio móvil de múltiples muestras, gestión energética *fail-safe* por desacoplamiento dinámico y un lazo de control adaptativo con control de permanencia temporal.

---

## 📑 Tabla de Contenidos
1. [Resumen Ejecutivo y Arquitectura del Subsistema](#-resumen-ejecutivo-y-arquitectura-del-subsistema)
2. [Especificaciones Técnicas del Hardware y Matriz de Pines](#-especificaciones-técnicas-del-hardware-y-matriz-de-pines)
3. [Matriz Comparativa de Variantes de Firmware](#-matriz-comparativa-de-variantes-de-firmware)
4. [Algoritmo de Filtrado y Lógica de Control Dinámico](#-algoritmo-de-filtrado-y-lógica-de-control-dinámico)
5. [Mitigación de Vibraciones y Gestión de Energía Fail-Safe](#-mitigación-de-vibraciones-y-gestión-de-energía-fail-safe)
6. [Guía Metodológica de Implementación y Despliegue](#-guía-metodológica-de-implementación-y-despliegue)

---

## 📌 Resumen Ejecutivo y Arquitectura del Subsistema

El proyecto **SentinelGuard** resuelve la apertura automatizada sin contacto mediante la emisión y recepción de pulsos acústicos de alta frecuencia a través de un transductor ultrasónico (**HC-SR04**). El sistema procesa las ondas reflejadas para determinar la proximidad del usuario, activando de forma síncrona un actuador angular (servomotor) bajo parámetros estrictos de temporización y seguridad ergonómica.

---

## 🛠️ Especificaciones Técnicas del Hardware y Matriz de Pines

El diseño circuital interconecta componentes discretos de control y potencia bajo la siguiente topología de asignación física:

| Periférico de Hardware | Pin / Conexión (Arduino) | Parámetro Funcional y Eléctrico |
| :--- | :--- | :--- |
| **Sensor Ultrasónico** | `trigPin` $\rightarrow$ Pin Digital 5 | Emisión de tren de pulsos ultrasónicos (Trigger) |
| **Sensor Ultrasónico** | `echoPin` $\rightarrow$ Pin Digital 6 | Recepción del eco de retorno (PulseIn width) |
| **Actuador Angular** | `servoPin` $\rightarrow$ Pin Digital 7 | Señal PWM de control para el servomotor |

---

## 📊 Matriz Comparativa de Variantes de Firmware

El repositorio cuenta con dos versiones de código adaptadas a diferentes escenarios operativas e industriales. A continuación se detalla la diferenciación técnica:

| Parámetro / Característica | Versión Base (`code sensor 5s - 80cm`) | Versión Avanzada Optimizada (`code sensor 3.5s - max cm`) |
| :--- | :--- | :--- |
| **Rango Geométrico de Detección** | Restringido de $30\text{ cm}$ a $80\text{ cm}$ | **Ampliado de $10\text{ cm}$ a $120\text{ cm}$** (Cobertura espacial superior y detección optimizada al 100%) |
| **Tiempo de Apertura Inicial** | Fijo e inalterable de $5000\text{ ms}$ ($5\text{ segundos}$) | **Dinamizado a $3500\text{ ms}$** ($3.5\text{ segundos}$ de espera mínima obligatoria) |
| **Control de Permanencia / Presencia** | Cierre automático estricto tras cumplirse el tiempo base | **Bucle de supervisión adaptativa (`while`)** de hasta $20\text{ segundos}$ adicionales que detecta si el usuario sigue presente o se retira anticipadamente |
| **Algoritmo de Cierre Inteligente** | Secuencial lineal sin reevaluación de entorno | **Confirmación anti-parpadeo** (Doble lectura de $300\text{ ms}$) para evitar falsos cierres por interferencia óptica o acústica |

---

## 🔄 Algoritmo de Filtrado y Lógica de Control Dinámico

1. **Filtrado por Promedio Móvil Temporal:**
   * Con el objeto de neutralizar falsos positivos derivados de reflexiones espurias o ruido electromagnético, el bucle principal efectúa tres mediciones secuenciales de distancia con un intervalo de $10\text{ ms}$, computando la media aritmética discreta:
     $$\bar{d} = \frac{\text{aver}[0] + \text{aver}[1] + \text{aver}[2]}{3}$$
2. **Lógica de Permanencia Adaptativa (`code sensor 3.5s - max cm`):**
   * Tras la apertura inicial de $3.5\text{ segundos}$, el firmware evalúa dinámicamente si el objeto permanece en el cono de cobertura. Si el usuario se retira antes de los $20\text{ segundos}$ límite, el sistema valida la ausencia mediante un re-muestreo de seguridad y ejecuta el cierre inmediato, optimizando los tiempos operativos del sistema biomecatrónico.

---

## 🛡️ Mitigación de Vibraciones y Gestión de Energía *Fail-Safe*

* **Desacoplamiento Dinámico en Reposo (`servo.detach()`):** Para evitar la fatiga térmica del circuito de control interno del servomotor y la generación de ruido audible o vibraciones parásitas, el firmware desacopla lógicamente la señal PWM del pin de salida al retornar a la posición base ($0^\circ$).
* **Posicionamiento Inicial de Seguridad:** Durante la rutina de inicialización (`setup()`), el eje se posiciona forzosamente en el origen antes de habilitar el acoplamiento de control, previniendo saltos mecánicos en los engranajes al energizar el sistema.

---

## 🚀 Guía Metodológica de Implementación y Despliegue

1. **Gestión de Dependencias:** Importe la librería nativa del entorno para el manejo de actuadores angulares: `#include <Servo.h>`.
2. **Validación del Mapeo Físico:** Verifique las conexiones del Trigger (Pin 5), Echo (Pin 6) y la línea PWM del servo (Pin 7).
3. **Selección del Archivo Fuente:** Cargue al microcontrolador la variante de su preferencia ubicada en el repositorio según los requerimientos de rango y tiempo de retención deseados.

---
<p align="center">Desarrollado bajo estándares avanzados de ingeniería ciberfísica e IoT.</p>
