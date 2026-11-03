# 🛡️ SentinelGuard: Sistema Biomecatrónico de Barrera Autónoma

Subsistema de sensado y actuación de precisión basado en la plataforma **Arduino**, orientado a la detección de obstáculos por telemetría ultrasónica y control cinemático de posicionamiento angular mediante servomotor. La arquitectura integra un algoritmo de filtrado por promedio móvil de múltiples muestras y un manejo dinámico de acoplamiento de potencia para mitigar la fluctuación mecánica (*jitter*).

---

## 📑 Tabla de Contenidos
1. [Resumen Ejecutivo y Arquitectura del Subsistema](#-resumen-ejecutivo-y-arquitectura-del-subsistema)
2. [Especificaciones Técnicas del Hardware y Matriz de Pines](#-especificaciones-técnicas-del-hardware-y-matriz-de-pines)
3. [Algoritmo de Filtrado y Lógica de Control Cinemático](#-algoritmo-de-filtrado-y-lógica-de-control-cinemático)
4. [Mitigación de Vibraciones y Gestión de Energía Fail-Safe](#-mitigación-de-vibraciones-y-gestión-de-energía-fail-safe)
5. [Guía Metodológica de Implementación y Despliegue](#-guía-metodológica-de-implementación-y-despliegue)

---

## 📌 Resumen Ejecutivo y Arquitectura del Subsistema

El firmware implementa un lazo de control condicional basado en la distancia métrica evaluada por un transductor ultrasónico (**HC-SR04** o compatible). Al registrarse un objeto dentro del umbral geométrico establecido ($30\text{ cm} \le d \le 80\text{ cm}$), el microcontrolador energiza dinámicamente el servomotor para ejecutar un barrido angular balístico, estabilizando la posición final antes de retornar al estado de reposo de alta eficiencia energética.

---

## 🛠️ Especificaciones Técnicas del Hardware y Matriz de Pines

El diseño del circuito interconecta componentes de lógica de control y actuación discretas con los siguientes parámetros de asignación física:

| Periférico de Hardware | Pin / Conexión (Arduino) | Parámetro Funcional y Eléctrico |
| :--- | :--- | :--- |
| **Sensor Ultrasónico** | `trigPin` $\rightarrow$ Pin Digital 5 | Emisión de tren de pulsos ultrasónicos (Trigger) |
| **Sensor Ultrasónico** | `echoPin` $\rightarrow$ Pin Digital 6 | Recepción del eco de retorno (PulseIn width) |
| **Actuador Angular** | `servoPin` $\rightarrow$ Pin Digital 7 | Señal PWM de control para el servomotor |

---

## 🔄 Algoritmo de Filtrado y Lógica de Control Cinemático

1. **Filtrado por Promedio Móvil Temporal:**
   * Con el objeto de neutralizar falsos positivos derivados de reflexiones espurias o ruido electromagnético, la función de ciclo principal efectúa tres mediciones secuenciales de distancia con un intervalo de $10\text{ ms}$, calculando la media aritmética discreta:
     $$\bar{d} = \frac{\text{aver}[0] + \text{aver}[1] + \text{aver}[2]}{3}$$
2. **Activación Condicional del Campo Vectorial:**
   * Si el valor resultante de $\bar{d}$ se encuentra confinado estrictamente entre el rango operativo ($30\text{ cm}$ y $80\text{ cm}$), el sistema inicializa la rutina de actuación mecánica.
3. **Control de Desplazamiento Gradual:**
   * El barrido angular se ejecuta mediante un bucle iterativo de incremento controlado de $0^\circ$ a $180^\circ$ con pasos de $5^\circ$ y un retardo de modulación de $5\text{ ms}$, garantizando una transición fluida y sin sobreimpulsos mecánicos de torque.

---

## 🛡️ Mitigación de Vibraciones y Gestión de Energía *Fail-Safe*

* **Desacoplamiento Dinámico en Reposo (`servo.detach()`):** Para evitar la fatiga del circuito de control interno del servomotor y la generación de ruido audible o vibraciones parásitas, el firmware desacopla lógicamente la señal PWM del pin de salida cuando el actuador se encuentra en su posición base ($0^\circ$).
* **Posicionamiento Inicial de Seguridad:** Durante la fase de inicialización (`setup()`), el eje se posiciona forzosamente en el origen angular antes de aplicar el acoplamiento de control, previniendo saltos de engranajes mecánicos al energizar el sistema.

---

## 🚀 Guía Metodológica de Implementación y Despliegue

Para integrar y validar este código en su entorno de desarrollo local, proceda conforme a las siguientes directrices operativas:

1. **Gestión de Dependencias del Compilador:**
   * Asegúrese de importar la librería nativa del entorno de desarrollo para el manejo de actuadores angulares: `#include <Servo.h>`.
2. **Validación del Mapeo Físico:**
   * Conecte el pin Trigger al digital 5, el Echo al digital 6 y la línea de control del servo al digital 7 de su placa de desarrollo basada en microcontrolador ATmega.
3. **Compilación y Transferencia:**
   * Transfiera el binario compilado a la placa mediante el IDE de Arduino y verifique la correcta lectura del puerto serial de depuración si requiere telemetría métrica en tiempo real.

---
<p align="center">Desarrollado bajo estándares avanzados de ingeniería ciberfísica e IoT.</p>
