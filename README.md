<p align="center">
  <img src="https://img.shields.io/badge/Platform-Arduino%20Uno-00979D?style=for-the-badge&logo=Arduino&logoColor=white" alt="Arduino">
  <img src="https://img.shields.io/badge/Language-C++-00599C?style=for-the-badge&logo=c%2++&logoColor=white" alt="C++">
  <img src="https://img.shields.io/badge/Hardware-HC--SR04%20%7C%20SG90-FF6F00?style=for-the-badge&logo=hardware&logoColor=white" alt="Hardware">
  <img src="https://img.shields.io/badge/Status-Verified%20%2F%2F%20Tinkercad-success?style=for-the-badge" alt="Status">
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge" alt="License">
</p>

# 🛡️ SentinelGuard: Sistema Biomecatrónico de Barrera y Automatización Autónoma

> **Subsistema avanzado de sensado y actuación de precisión** basado en la plataforma **Arduino Uno**, optimizado e implementado exactamente de acuerdo con el diagrama circuital validado en entorno de simulación física. Diseñado para la automatización inteligente de contenedores de residuos, dispensadores sanitarios y accesos perimetrales mediante telemetría ultrasónica y control cinemático de posicionamiento angular.

---

## 📑 Tabla de Contenidos
1. [Análisis del Diagrama Físico y Matriz de Conexiones](#-1-análisis-del-diagrama-físico-y-matriz-de-conexiones)
2. [Arquitectura y Principio de Funcionamiento](#-2-arquitectura-y-principio-de-funcionamiento)
3. [Matriz Comparativa de Variantes de Firmware](#-3-matriz-comparativa-de-variantes-de-firmware)
4. [Catálogo Extendido de 10 Proyectos y Adaptaciones de Código](#-4-catálogo-extendido-de-10-proyectos-y-adaptaciones-de-código)
5. [Algoritmo de Filtrado y Lógica de Control Dinámico](#-5-algoritmo-de-filtrado-y-lógica-de-control-dinámico)
6. [Gestión de Energía y Mitigación de Vibraciones (Fail-Safe)](#-6-gestión-de-energía-y-mitigación-de-vibraciones-fail-safe)
7. [Guía Metodológica de Implementación y Despliegue](#-7-guía-metodológica-de-implementación-y-despliegue)

---

## 🔌 1. Análisis del Diagrama Físico y Matriz de Conexiones

Conforme al diagrama esquemático validado visualmente, la topología del circuito interconecta los elementos electrónicos asegurando que todas las líneas de alimentación positiva ($V_{cc}$) converjan de forma directa en el riel de **5V** y las líneas de retorno a tierra en **GND**.

| Componente Físico | Pin del Periférico | Pin en Arduino Uno | Color del Cable en Diagrama | Parámetro Funcional y Eléctrico |
| :--- | :--- | :--- | :--- | :--- |
| **Sensor HC-SR04** | `Vcc` | `5V` | Rojo | Alimentación regulada del transductor ultrasónico |
| **Sensor HC-SR04** | `Trig` | **Pin Digital 5** | Amarillo | Disparo del tren de pulsos acústicos |
| **Sensor HC-SR04** | `Echo` | **Pin Digital 6** | Naranja | Recepción del eco de retorno (Ancho de pulso) |
| **Sensor HC-SR04** | `GND` | `GND` | Negro | Retorno a tierra lógica |
| **Servomotor SG90** | Potencia ($V_{cc}$) | `5V` | Rojo | Alimentación positiva del motor |
| **Servomotor SG90** | Control PWM | **Pin Digital 7** | Verde | Señal de posicionamiento angular |
| **Servomotor SG90** | Tierra (GND) | `GND` | Negro | Retorno a tierra de potencia |

---

## 🏗️ 2. Arquitectura y Principio de Funcionamiento

El sistema opera mediante un ciclo de control embebido en tiempo real:
* **Sensado Acústico de Alta Fidelidad:** Emisión de trenes de pulsos ultrasónicos a $40\text{ kHz}$ mediante el transductor **HC-SR04** conectado a los pines digitales 5 y 6.
* **Procesamiento y Filtrado Digital:** Implementación de un promedio móvil de 3 muestras para suprimir ruidos electromagnéticos y reflexiones falsas del entorno.
* **Actuación Dinámica y Ahorro Energético:** Activación síncrona del servomotor (Pin 7) bajo un lazo de permanencia adaptativo, desacoplando la señal PWM (`servo.detach()`) durante los periodos de reposo para eliminar zumbidos y fatiga térmica en los engranajes.

---

## 📊 3. Matriz Comparativa de Variantes de Firmware

El repositorio incluye versiones especializadas de código fuente adaptadas a las exigencias operativas del entorno físico:

| Parámetro / Característica | Versión Base (`code sensor 5s - 80cm`) | Versión Avanzada Adaptativa (`code sensor 5s - 20s`) |
| :--- | :--- | :--- |
| **Rango Geométrico** | Restringido de $30\text{ cm}$ a $80\text{ cm}$ | **Ampliado de $10\text{ cm}$ a $120\text{ cm}$ (Sensibilidad 100%)** |
| **Tiempo de Retención** | Fijo de $5000\text{ ms}$ ($5\text{ segundos}$) | $5\text{ segundos}$ iniciales + **hasta $20\text{ segundos}$ dinámicos** |
| **Lógica de Cierre** | Secuencial lineal estricta | **Cierre inteligente anticipado ($1\text{ s}$ de gracia)** ante la retirada del usuario |

---

## 💡 4. Catálogo Extendido de 10 Proyectos y Adaptaciones de Código

Basado en la circuitería del diagrama de referencia, el núcleo de **SentinelGuard** puede configurarse para múltiples propósitos tecnológicos:

### 1. 🚗 Barrera de Estacionamiento Inteligente
* **Título:** Barrera de estacionamiento inteligente por detección ultrasónica.
* **Descripción:** El sensor ultrasónico detecta la presencia de un vehículo a corta distancia y activa el servomotor para levantar una pequeña barrera o compuerta, cerrándola automáticamente tras unos segundos.
* **Modificación en Código:** Ajustar el ángulo de apertura a $90^\circ$ (`servo.write(90)`) y ampliar el tiempo de espera base a 8 segundos.

### 2. 🗑️ Papelera Automatizada sin Contacto (Smart Bin)
* **Título:** Tapa de contenedor con apertura por proximidad.
* **Descripción:** Al acercar la mano o un objeto al frente del sensor ultrasónico, el sistema acciona el servomotor conectado a la tapa del recipiente para abrirla de forma fluida y autónoma.
* **Modificación en Código:** Emplear la variante avanzada (`code sensor 5s - 20s`) para mantener la tapa abierta mientras se desechan residuos y cerrarla instantáneamente al retirar la mano.

### 3. 🚨 Radar de Retroceso Sonoro y Visual
* **Título:** Sistema de alarma y advertencia de distancia crítica.
* **Descripción:** Mide la distancia de un objeto en reversa; cuando este sobrepasa un límite de seguridad, el servo mueve una aguja indicadora o activa un mecanismo de advertencia.
* **Modificación en Código:** Mapear la distancia leída directamente al ángulo del servo mediante `map(dist, 10, 100, 0, 180);`.

### 4. 🧴 Dispensador Higiénico de Gel o Alimento
* **Título:** Dispensador automatizado de alimento o gel.
* **Descripción:** Detecta la colocación de un recipiente o mano bajo la boquilla para que el servomotor gire un ángulo determinado, accionando una palanca que libera una porción de líquido o producto.
* **Modificación en Código:** Reducir el rango de activación a un umbral estricto de $5\text{ cm}$ a $15\text{ cm}$.

### 5. 📡 Escáner Bidimensional de Obstáculos (Mini Radar)
* **Título:** Mapeo de entornos mediante barrido ultrasónico.
* **Descripción:** El servomotor rota continuamente la posición del sensor ultrasónico de lado a lado para medir distancias en un arco de 180°, enviando los datos para generar un gráfico de radar en pantalla.
* **Modificación en Código:** Sustituir la condición de presencia por un bucle continuo de barrido con bucles `for` que reporten telemetría por puerto serial.

### 6. 🌬️ Deflector de Aire Acondicionado Inteligente
* **Título:** Sistema de ventilación o dirección de flujo reactivo.
* **Descripción:** Un sensor detecta la cercanía de una persona frente al dispositivo y ajusta el ángulo de inclinación de una compuerta móvil mediante el servo para direccionar un flujo de aire.
* **Modificación en Código:** Vincular la posición angular del deflector a rangos específicos de distancia leídos en tiempo real.

### 7. 🎯 Blanco Móvil Interactivo (Diana de Tiro)
* **Título:** Juego educativo de precisión (Diana móvil).
* **Descripción:** El sensor detecta si un objeto se aproxima rápidamente (como un proyectil de juguete), haciendo que el servomotor mueva o esconda instantáneamente un blanco u objetivo.
* **Modificación en Código:** Añadir una interrupción por umbral ultra-corto ($< 15\text{ cm}$) para accionar una respuesta balística inmediata.

### 8. 💡 Luminaria con Dimmer Mecánico
* **Título:** Lámpara de activación por presencia cercana.
* **Descripción:** Al detectar la presencia de una persona cerca del escritorio, el sensor activa la iluminación y un mecanismo de espejo o persiana operado por el servo cambia la dirección de la luz.
* **Modificación en Código:** Activar un módulo de relé de manera simultánea con el acoplamiento del servomotor.

### 9. 🪪 Torniquete de Acceso Automatizado a Escala
* **Título:** Control automático de ingreso en maquetas didácticas.
* **Descripción:** Simula la entrada a una estación o edificio; el sensor valida la cercanía del usuario y el servomotor gira el brazo del torniquete permitiendo el paso por tiempo limitado.
* **Modificación en Código:** Incrementar un contador de variables enteras cada vez que se complete un ciclo de apertura y cierre.

### 10. 🚙 Sistema Anticolisión para Miniaturas Móviles
* **Título:** Detector de obstáculos con respuesta de evasión direccional.
* **Descripción:** Simula el sistema de dirección autónoma de un rover; el sensor escanea el frente y, ante un obstáculo cercano, el servo gira un mecanismo frontal para desviar el curso o señalar la ruta libre.
* **Modificación en Código:** Integrar la lógica ultrasónica con controladores de motores de corriente continua (puente H).

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
2. **Validación de Pines:** Conecte rigurosamente según el diagrama físico: Trigger al Pin 5, Echo al Pin 6, y la señal PWM del servo al Pin 7, asegurando que todos los cables positivos ($V_{cc}$) vayan conectados al riel de **5V**.
3. **Destello (*Flashing*):** Seleccione la variante de firmware adecuada a su proyecto y cargue el binario a su placa Arduino Uno.

---
<p align="center">Desarrollado bajo estándares avanzados de ingeniería ciberfísica e IoT.</p>
