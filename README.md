# 🛡️ SentinelGuard: Sistema Biomecatrónico de Barrera Autónoma

Subsistema avanzado de sensado y actuación de precisión basado en la plataforma **Arduino**, optimizado para la automatización inteligente de contenedores, dispensadores sanitarios y accesos perimetrales mediante telemetría ultrasónica y control cinemático de posicionamiento angular. La arquitectura integra un algoritmo de filtrado por promedio móvil de múltiples muestras, gestión energética *fail-safe* por desacoplamiento dinámico y un lazo de control adaptativo con supervisión de permanencia temporal.

---

## 📑 Tabla de Contenidos
1. [Resumen Ejecutivo y Arquitectura del Subsistema](#-resumen-ejecutivo-y-arquitectura-del-subsistema)
2. [Especificaciones Técnicas del Hardware y Matriz de Pines](#-specificaciones-técnicas-del-hardware-y-matriz-de-pines)
3. [Matriz Comparativa de Variantes de Firmware](#-matriz-comparativa-de-variantes-de-firmware)
4. [Casos de Uso e Implementaciones Prácticas](#-casos-de-uso-e-implementaciones-prácticas)
5. [Algoritmo de Filtrado y Lógica de Control Dinámico](#-algoritmo-de-filtrado-y-lógica-de-control-dinámico)
6. [Mitigación de Vibraciones y Gestión de Energía Fail-Safe](#-mitigación-de-vibraciones-y-gestión-de-energía-fail-safe)
7. [Guía Metodológica de Implementación y Despliegue](#-guía-metodológica-de-despliegue-e-despliegue)

---

## 📌 Resumen Ejecutivo y Arquitectura del Subsistema

El proyecto **SentinelGuard** resuelve la apertura automatizada sin contacto físico mediante la emisión y recepción de pulsos acústicos de alta frecuencia a través de un transductor ultrasónico (**HC-SR04**). El sistema procesa las ondas reflejadas para determinar la proximidad del usuario, activando de forma síncrona un actuador angular (servomotor) bajo parámetros estrictos de temporización, alcance espacial optimizado y control ergonómico de presencia.

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

## 💡 4. Casos de Uso e Implementaciones Prácticas

Gracias a su diseño modular y su lazo de control de alta precisión, **SentinelGuard** puede ser adaptado a múltiples entornos del mundo real:

### 🗑️ A. Contenedores de Residuos Inteligentes (*Smart Trash Bins*)
* **Aplicación:** Automatización de tapas en papeleras domésticas, hospitalarias o industriales.
* **Ventaja Operativa:** Evita el contacto físico con superficies potencialmente contaminadas. La variante avanzada (`code sensor 5s - 20s`) permite mantener la tapa abierta de manera fluida si el usuario está depositando múltiples objetos, cerrándose de forma automática e inmediata (tras 1 segundo de ausencia) una vez que la persona se retira.

### 🧴 B. Dispensadores Automáticos de Agentes Sanitizantes
* **Aplicación:** Activación mecánica de válvulas de presión en dispensadores de alcohol en gel o jabón líquido.
* **Ventaja Operativa:** El rango ampliado de detección ($10\text{ cm}$ a $120\text{ cm}$) detecta las manos del usuario de manera inmediata al
