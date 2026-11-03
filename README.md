# 🛡️ SentinelGuard: Sistema Biomecatrónico de Barrera Autónoma

Subsistema avanzado de sensado y actuación de precisión basado en la plataforma **Arduino**, optimizado para la automatización inteligente de contenedores o barreras perimetrales mediante telemetría ultrasónica y control cinemático de posicionamiento angular. La arquitectura integra un algoritmo de filtrado por promedio móvil de múltiples muestras, gestión energética *fail-safe* por desacoplamiento dinámico y un lazo de control adaptativo de alta fidelidad con supervisión de permanencia temporal.

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

El proyecto **SentinelGuard** resuelve la apertura automatizada sin contacto mediante la emisión y recepción de pulsos acústicos de alta frecuencia a través de un transductor ultrasónico (**HC-SR04**). El sistema procesa las ondas reflejadas para determinar la proximidad del usuario, activando de forma síncrona un actuador angular (servomotor) bajo parámetros estrictos de temporización, alcance espacial optimizado y control ergonómico de presencia.

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

El repositorio cuenta con dos versiones evolutivas de código adaptadas a diferentes escenarios de rendimiento operativo:

| Parámetro / Característica | Versión Estándar (`code sensor 5s - 80cm`) | Versión Optimizada Avanzada (`code sensor 5s - 20s`) |
| :--- | :--- | :--- |
| **Rango Geométrico de Detección** | Restringido de $30\text{ cm}$ a $80\text{ cm}$ | **Ampliado de $10\text{ cm}$ a $120\text{ cm}$** (Mayor cobertura de proximidad y sensibilidad óptima) |
| **Tiempo de Apertura Inicial** | Fijo de $5000\text{ ms}$ ($5\text{ segundos}$) | **Fijo inicial de $5000\text{ ms}$** ($5\text{ segundos}$ de retención obligatoria al abrir) |
| **Lógica de Permanencia / Presencia** | Cierre automático lineal inmediato al cumplirse el tiempo base | **Bucle de supervisión dinámica con temporizador flotante (`millis()`)** que evalúa presencia hasta por un límite de $20\text{ segundos}$ |
| **Estrategia de Cierre Adaptativo** | Secuencial sin reevaluación del entorno activo | **Cierre inteligente anticipado ($1\text{ segundo}$ de gracia)** si la persona se retira antes de agotar el tiempo máximo |

---

## 🔄 Algoritmo de Filtrado y Lógica de Control Dinámico

1. **Filtrado por Promedio Móvil Temporal:**
   * Con el objeto de neutralizar falsos positivos derivados de reflexiones espurias o ruido electromagnético, el ciclo de lectura efectúa tres mediciones secuenciales con un intervalo de $10\text{ ms}$, computando la media aritmética discreta:
     $$\bar{d} = \frac{\text{aver}[0] + \text{aver}[1] + \text{aver}[2]}{3}$$
2. **Supervisión Dinámica de Presencia (`code sensor 5s - 20s`):**
   * Tras cumplirse el tiempo de retención inicial, el firmware ejecuta un lazo condicional basado en la función `millis()`. Si el usuario continúa interactuando dentro del rango ($10\text{ cm}$ a $120\text{ cm}$), la marca de tiempo se actualiza de forma fluida. En caso de detectarse la ausencia del usuario por un lapso superior a $1\text{ segundo}$, el sistema interrumpe el ciclo de espera y procede al cierre inmediato de la estructura.

---

## 🛡️ Mitigación de Vibraciones y Gestión de Energía *Fail-Safe*

* **Desacoplamiento Dinámico en Reposo (`servo.detach()`):** Para evitar la fatiga térmica del circuito de control interno del servomotor y la generación de ruido audible o vibraciones parásitas, el firmware desacopla lógicamente la señal PWM del pin de salida al retornar a la posición base ($0^\circ$).
* **Posicionamiento Inicial de Seguridad:** Durante la rutina de inicialización (`setup()`), el eje se posiciona forzosamente en el origen antes de habilitar el acoplamiento de control, previniendo saltos mecánicos en los engranajes al energizar el sistema.

---

## 🚀 Guía Metodológica de Implementación y Despliegue

1. **Gestión de Dependencias:** Asegúrese de importar la librería nativa del entorno de desarrollo para el manejo de actuadores angulares: `#include <Servo.h>`.
2. **Validación del Mapeo Físico:** Verifique la correcta asignación de pines: Trigger (Pin 5), Echo (Pin 6) y la línea de control PWM del servo (Pin 7).
3. **Selección de Variante:** Cargue al microcontrolador el archivo de código fuente de su preferencia según los parámetros de rango y persistencia temporal requeridos en su aplicación.

---
<p align="center">Desarrollado bajo estándares avanzados de ingeniería ciberfísica e IoT.</p>
