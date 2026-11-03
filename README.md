<p align="center">
  <img src="https://img.shields.io/badge/Platform-Arduino-00979D?style=for-the-badge&logo=Arduino&logoColor=white" alt="Arduino">
  <img src="https://img.shields.io/badge/Language-C++-00599C?style=for-the-badge&logo=c%2++&logoColor=white" alt="C++">
  <img src="https://img.shields.io/badge/Hardware-HC--SR04%20%7C%20SG90-FF6F00?style=for-the-badge&logo=hardware&logoColor=white" alt="Hardware">
  <img src="https://img.shields.io/badge/Status-Stable%20%2F%2F%20Production-success?style=for-the-badge" alt="Status">
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge" alt="License">
</p>

# 🛡️ SentinelGuard: Sistema Biomecatrónico de Barrera y Automatización Autónoma

> **Subsistema avanzado de sensado y actuación de precisión** basado en la plataforma **Arduino**, optimizado para la automatización inteligente de contenedores de residuos, dispensadores sanitarios, barreras de estacionamiento y accesos perimetrales mediante telemetría ultrasónica y control cinemático de posicionamiento angular.

---

## 📑 Tabla de Contenidos
1. [Arquitectura y Principio de Funcionamiento](#-arquitectura-y-principio-de-funcionamiento)
2. [Matriz de Conexiones Físicas (Pinout)](#-matriz-de-conexiones-físicas-pinout)
3. [Matriz Comparativa de Variantes de Firmware](#-matriz-comparativa-de-variantes-de-firmware)
4. [Catálogo Extendido de 10 Proyectos y Adaptaciones de Código](#-catálogo-extendido-de-10-proyectos-y-adaptaciones-de-código)
5. [Algoritmo de Filtrado y Lógica de Control Dinámico](#-algoritmo-de-filtrado-y-lógica-de-control-dinámico)
6. [Gestión de Energía y Mitigación de Vibraciones (Fail-Safe)](#-gestión-de-energia-y-mitigación-de-vibraciones-fail-safe)
7. [Guía Metodológica de Implementación y Despliegue](#-guía-metodológica-de-implementación-y-despliegue)

---

## 🏗️ 1. Arquitectura y Principio de Funcionamiento

El sistema opera mediante un ciclo de control embebido en tiempo real:
* **Sensado Acústico de Alta Fidelidad:** Emisión de trenes de pulsos ultrasónicos a $40\text{ kHz}$ mediante el transductor **HC-SR04**.
* **Procesamiento y Filtrado Digital:** Implementación de un promedio móvil de 3 muestras para suprimir ruidos electromagnéticos y reflexiones falsas.
* **Actuación Dinámica y Ahorro Energético:** Activación síncrona del servomotor bajo un lazo de permanencia adaptativo, desacoplando la señal PWM (`servo.detach()`) durante los periodos de reposo para eliminar zumbidos y fatiga térmica.

---

## 🔌 2. Matriz de Conexiones Físicas (Pinout)

Esquema de interconexión circuital estándar para el despliegue del hardware:

| Periférico de Hardware | Pin / Señal | Pin en Arduino | Parámetro Funcional y Eléctrico |
| :--- | :--- | :--- | :--- |
| **Sensor HC-SR04** | `VCC` | `5V` | Alimentación regulada del transductor |
| **Sensor HC-SR04** | `GND` | `GND` | Tierra lógica común |
| **Sensor HC-SR04** | `Trig` | **Pin Digital 5** | Disparo de pulsos ultrasónicos |
| **Sensor HC-SR04** | `Echo` | **Pin Digital 6** | Recepción de la onda de eco reflejada |
| **Servomotor (SG90/MG995)**| `PWM` | **Pin Digital 7** | Señal de control angular del motor |

---

## 📊 3. Matriz Comparativa de Variantes de Firmware

El repositorio incluye versiones especializadas según las exigencias operativas del entorno:

| Parámetro / Característica | Versión Base (`code sensor 5s - 80cm`) | Versión Avanzada Adaptativa (`code sensor 5s - 20s`) |
| :--- | :--- | :--- |
| **Rango Geométrico** | Restringido de $30\text{ cm}$ a $80\text{ cm}$ | **Ampliado de $10\text{ cm}$ a $120\text{ cm}$ (Sensibilidad 100%)** |
| **Tiempo de Retención** | Fijo de $5000\text{ ms}$ ($5\text{ segundos}$) | $5\text{ segundos}$ iniciales + **hasta $20\text{ segundos}$ dinámicos** |
| **Lógica de Cierre** | Secuencial lineal estricta | **Cierre inteligente anticipado ($1\text{ s}$ de gracia)** ante la retirada del usuario |

---

## 💡 4. Catálogo Extendido de 10 Proyectos y Adaptaciones de Código

El núcleo de **SentinelGuard** puede configurarse para distintos propósitos modificando parámetros lógicos y periféricos auxiliares:

### 1. 🚗 Barrera de Estacionamiento Inteligente
* **Descripción:** Detecta la presencia de un vehículo a corta distancia y activa el servomotor para levantar una barrera física, cerrándola automáticamente tras el paso del móvil.
* **Modificación en Código:** Ajustar el ángulo de apertura a $90^\circ$ (`servo.write(90)`) y ampliar el tiempo de espera base a 8 segundos.
* **Hardware Extra:** LEDs indicadores de paso (Verde/Rojo) conectados a pines digitales adicionales.

### 2. 🗑️ Papelera Automatizada sin Contacto (Smart Bin)
* **Descripción:** Acciona la tapa superior de un recipiente al detectar la proximidad de la mano del usuario de manera fluida e higiénica.
* **Modificación en Código:** Emplear la variante avanzada (`code sensor 5s - 20s`) para mantener la tapa abierta mientras se desechan residuos y cerrarla instantáneamente al retirar la mano.
* **Hardware Extra:** Estructura plástica contenedora y mecanismo de varilla o polea en el eje del servo.

### 3. 🚨 Radar de Retroceso Sonoro y Visual
* **Descripción:** Sistema de advertencia perimetral en reversa; al disminuir la distancia crítica, el servo mueve una aguja indicadora en un panel graduado mientras emite alertas acústicas.
* **Modificación en Código:** Mapear la distancia leída directamente al ángulo del servo: `int angulo = map(dist, 10, 100, 0, 180); servo.write(angulo);`.
* **Hardware Extra:** Buzzer activo y matriz de diodos LED de advertencia.

### 4. 🧴 Dispensador Higiénico de Gel o Alimento
* **Descripción:** Al colocar un recipiente bajo la boquilla, el servomotor gira un ángulo determinado para presionar una palanca dosificadora.
* **Modificación en Código:** Reducir el rango de activación a un umbral estricto de $5\text{ cm}$ a $15\text{ cm}$ para evitar activaciones accidentales.
* **Hardware Extra:** Soporte mecánico de palanca y módulo de alimentación externa para el motor.

### 5. 📡 Escáner Bidimensional de Obstáculos (Mini Radar)
* **Descripción:** Rota continuamente el sensor ultrasónico en un arco de $180^\circ$ para mapear el entorno físico y graficar los datos en la terminal.
* **Modificación en Código:** Sustituir el bucle condicional de presencia por un barrido bidireccional continuo con bucles `for` anidados enviando datos por puerto serial (`Serial.print`).
* **Hardware Extra:** Cable USB para telemetría en tiempo real y base móvil rotativa.

### 6. 🌬️ Deflector de Aire Acondicionado Inteligente
* **Descripción:** Direcciona un flujo de aire móvil cambiando la inclinación de una compuerta al detectar la presencia de una persona frente al dispositivo.
* **Modificación en Código:** Vincular la posición angular del deflector a rangos específicos de distancia detectada por el sensor.
* **Hardware Extra:** Mini ventilador DC controlado mediante transistor 2N2222 y compuerta deflectora.

### 7. 🎯 Blanco Móvil Interactivo (Diana de Tiro)
* **Descripción:** Un sensor detecta la aproximación rápida de un objeto (proyectil de juguete), haciendo que el servomotor oculte o mueva instantáneamente el objetivo.
* **Modificación en Código:** Añadir una interrupción por umbral ultra-corto (< 15 cm) para accionar una respuesta balística inmediata del servo.
* **Hardware Extra:** Estructura de impacto y pulsador de reinicio físico.

### 8. 💡 Luminaria con Dimmer Mecánico
* **Descripción:** Al detectar presencia cercana en un escritorio, un sistema accionado por el servo ajusta la posición de una persiana o espejo para redirigir el haz lumínico.
* **Modificación en Código:** Activar un módulo de relé de manera simultánea con el acoplamiento del servomotor.
* **Hardware Extra:** Módulo de relé de 1 canal y bombilla LED de baja potencia.

### 9. 🪪 Torniquete de Acceso Automatizado a Escala
* **Descripción:** Simula el control de ingreso a una estación; valida la proximidad y gira un brazo mecánico por tiempo limitado, contabilizando los accesos en pantalla.
* **Modificación en Código:** Incrementar un contador de variables enteras cada vez que se complete un ciclo de apertura y cierre.
* **Hardware Extra:** Display LCD 16x2 con interfaz I2C.

### 10. 🚙 Sistema Anticolisión para Miniaturas Móviles
* **Descripción:** Simula la dirección autónoma de un rover; ante un obstáculo frontal cercano, desvía un mecanismo de dirección o frena la marcha.
* **Modificación en Código:** Integrar la lógica del sensor ultrasónico con controladores de motores de corriente continua (puente H).
* **Hardware Extra:** Chasis de pruebas robótico y soporte de baterías.

---

## 🔄 5. Algoritmo de Filtrado y Lógica de Control Dinámico

1. **Filtrado por Promedio Móvil Temporal:**
   * Para neutralizar falsos positivos por ruido acústico, se efectúan tres lecturas consecutivas con un intervalo de $10\text{ ms}$:
     $$\bar{d} = \frac{\text{aver}[0] + \text{aver}[1] + \text{aver}[2]}{3}$$
2. **Supervisión de Presencia Adaptativa:**
   * Utiliza la función de tiempo flotante `millis()` para mantener un monitoreo activo de hasta $20\text{ segundos}$, garantizando un funcionamiento ergonómico y eficiente.

---

## 🛡️ 6. Gestión de Energía y Mitigación de Vibraciones (*Fail-Safe*)

* **Desacoplamiento Dinámico en Reposo (`servo.detach()`):** Evita la fatiga térmica del circuito interno del motor y la generación de zumbidos audibles al retornar a la posición base ($0^\circ$).
* **Posicionamiento Inicial de Seguridad:** Fuerza el eje al origen durante la inicialización (`setup()`) para prevenir saltos de engranajes mecánicos.

---

## 🚀 7. Guía Metodológica de Implementación y Despliegue

1. **Dependencias:** Importe la librería nativa `#include <Servo.h>` en su entorno **Arduino IDE**.
2. **Validación de Pines:** Conecte el Trigger al Pin 5, el Echo al Pin 6 y la señal PWM del servo al Pin 7.
3. **Destello (*Flashing*):** Seleccione la variante de firmware adecuada a su proyecto y cargue el binario a la placa de desarrollo.

---
<p align="center">Desarrollado bajo estándares avanzados de ingeniería ciberfísica e IoT.</p>
