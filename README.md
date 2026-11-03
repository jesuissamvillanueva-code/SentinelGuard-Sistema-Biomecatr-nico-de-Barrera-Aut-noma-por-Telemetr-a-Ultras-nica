# 🛡️ SentinelGuard: Sistema Biomecatrónico de Barrera y Automatización Autónoma

Subsistema avanzado de sensado y actuación de precisión basado en la plataforma **Arduino**, optimizado para la automatización inteligente de contenedores de residuos, dispensadores sanitarios, barreras de estacionamiento y accesos perimetrales mediante telemetría ultrasónica y control cinemático de posicionamiento angular. La arquitectura integra un algoritmo de filtrado por promedio móvil de múltiples muestras, gestión energética *fail-safe* por desacoplamiento dinámico y un lazo de control adaptativo con supervisión de permanencia temporal.

---

## 📑 Tabla de Contenidos
1. [Resumen Ejecutivo y Arquitectura del Subsistema](#-resumen-ejecutivo-y-arquitectura-del-subsistema)
2. [Especificaciones Técnicas del Hardware y Matriz de Pines](#-especificaciones-técnicas-del-hardware-y-matriz-de-pines)
3. [Matriz Comparativa de Variantes de Firmware](#-matriz-comparativa-de-variantes-de-firmware)
4. [Catálogo Extendido de Casos de Uso e Implementaciones Prácticas](#-catálogo-extendido-de-casos-de-uso-e-implementaciones-prácticas)
5. [Algoritmo de Filtrado y Lógica de Control Dinámico](#-algoritmo-de-filtrado-y-lógica-de-control-dinámico)
6. [Mitigación de Vibraciones y Gestión de Energía Fail-Safe](#-mitigación-de-vibraciones-y-gestión-de-energía-fail-safe)
7. [Guía Metodológica de Implementación y Despliegue](#-guía-metodológica-de-implementación-y-despliegue)

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

## 💡 Catálogo Extendido de Casos de Uso e Implementaciones Prácticas

Gracias a la flexibilidad de su circuitería y su lazo de control adaptativo, el núcleo de **SentinelGuard** puede ser adaptado a 10 aplicaciones ciberfísicas de alto impacto:

### 1. Barrera de Estacionamiento Inteligente
* **Título:** Barrera de estacionamiento inteligente por detección ultrasónica.
* **Descripción:** El sensor ultrasónico detecta la presencia de un vehículo a corta distancia y activa el servomotor para levantar una pequeña barrera o compuerta, cerrándola automáticamente tras unos segundos.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, LEDs indicadores, resistencias y cables jumper.

### 2. Papelera Automatizada sin Contacto
* **Título:** Tapa de contenedor con apertura por proximidad.
* **Descripción:** Al acercar la mano o un objeto al frente del sensor ultrasónico, el sistema acciona el servomotor conectado a la tapa del recipiente para abrirla de forma fluida y autónoma.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, estructura plástica/recipiente y cables de conexión.

### 3. Radar de Retroceso Sonoro y Visual
* **Título:** Sistema de alarma y advertencia de distancia crítica.
* **Descripción:** Mide la distancia de un objeto en reversa; cuando este sobrepasa un límite de seguridad, el servo mueve una aguja indicadora o activa un mecanismo de advertencia.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, buzzer activo y diodos LED.

### 4. Despachador Higiénico por Proximidad
* **Título:** Dispensador automatizado de alimento o gel.
* **Descripción:** Detecta la colocación de un recipiente o mano bajo la boquilla para que el servomotor gire un ángulo determinado, accionando una palanca que libera una porción de líquido o producto.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, soporte mecánico y módulo de alimentación externa.

### 5. Escáner Bidimensional de Obstáculos (Mini Radar)
* **Título:** Mapeo de entornos mediante barrido ultrasónico.
* **Descripción:** El servomotor rota continuamente la posición del sensor ultrasónico de lado a lado para medir distancias en un arco de 180°, enviando los datos para generar un gráfico de radar en pantalla.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90 y cable USB para telemetría.

### 6. Deflector de Aire Acondicionado Inteligente
* **Título:** Sistema de ventilación o dirección de flujo reactivo.
* **Descripción:** Un sensor detecta la cercanía de una persona frente al dispositivo y ajusta el ángulo de inclinación de una compuerta móvil mediante el servo para direccionar un flujo de aire.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, mini ventilador DC y transistor 2N2222 (control de potencia).

### 7. Blanco Móvil Interactivo
* **Título:** Juego educativo de precisión (Diana móvil).
* **Descripción:** El sensor detecta si un objeto se aproxima rápidamente (como un proyectil de juguete), haciendo que el servomotor mueva o esconda instantáneamente un blanco u objetivo.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, estructura de cartón/madera y pulsador de reinicio.

### 8. Luminaria con Dimmer Mecánico
* **Título:** Lámpara de activación por presencia cercana.
* **Descripción:** Al detectar la presencia de una persona cerca del escritorio, el sensor activa la iluminación y un mecanismo de espejo o persiana operado por el servo cambia la dirección de la luz.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, módulo de relé y bombilla LED de baja potencia.

### 9. Torniquete de Acceso Automatizado a Escala
* **Título:** Control automático de ingreso en maquetas didácticas.
* **Descripción:** Simula la entrada a una estación o edificio; el sensor valida la cercanía del usuario y el servomotor gira el brazo del torniquete permitiendo el paso por tiempo limitado.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90 y display LCD 16x2 I2C (para conteo de accesos).

### 10. Sistema Anticolisión para Miniaturas Móviles
* **Título:** Detector de obstáculos con respuesta de evasión direccional.
* **Descripción:** Simula el sistema de dirección autónoma de un rover; el sensor escanea el frente y, ante un obstáculo cercano, el servo gira un mecanismo frontal para desviar el curso o señalar la ruta libre.
* **Componentes Clave:** Arduino Uno, Sensor HC-SR04, Servomotor SG90, chasis de prueba y portapilas.

---

## 🔄 Algoritmo de Filtrado y Lógica de Control Dinámico

1. **Filtrado por Promedio Móvil Temporal:**
   * Con el objeto de neutralizar falsos positivos derivados de reflexiones espurias o ruido electromagnético, el ciclo de lectura efectúa tres mediciones secuenciales con un intervalo de $10\text{ ms}$, computando la media aritmética discreta:
     $$\bar{d} = \frac{\text{aver}[0] + \text{aver}[1] + \text{aver}[2]}{3}$$
2. **Supervisión Dinámica de Presencia (`code sensor 5s - 20s`):**
   * Tras cumplirse el tiempo de retención inicial de $5\text{ segundos}$, el firmware ejecuta un lazo condicional basado en la función de tiempo flotante `millis()`. Si el usuario continúa interactuando dentro del cono de cobertura ($10\text{ cm}$ a $120\text{ cm}$), la marca de tiempo se actualiza de manera continua. Si se registra la ausencia del operador por un lapso superior a $1\text{ segundo}$, el sistema interrumpe el ciclo de espera y ejecuta el retorno inmediato de la estructura.

---

## 🛡️ Mitigación de Vibraciones y Gestión de Energía *Fail-Safe*

* **Desacoplamiento Dinámico en Reposo (`servo.detach()`):** Para evitar la fatiga térmica del circuito de control interno del servomotor y la generación de ruido audible o vibraciones parásitas, el firmware desacopla lógicamente la señal PWM del pin de salida al retornar a la posición base ($0^\circ$).
* **Posicionamiento Inicial de Seguridad:** Durante la rutina de inicialización (`setup()`), el eje se posiciona forzosamente en el origen antes de habilitar el acoplamiento de control, previniendo saltos mecánicos en los engranajes al energizar el sistema.

---

## 🚀 Guía Metodológica de Implementación y Despliegue

Para replicar y desplegar esta infraestructura en su entorno de desarrollo, proceda según las siguientes directrices operativas:

1. **Gestión de Dependencias:**
   * Asegúrese de importar la librería nativa del entorno de desarrollo para el manejo de actuadores angulares: `#include <Servo.h>`.
2. **Validación del Mapeo Físico:**
   * Verifique la correcta asignación de pines físicos: Trigger (Pin 5), Echo (Pin 6) y la línea de control PWM del servo (Pin 7).
3. **Selección de Variante en el Repositorio:**
   * Cargue al microcontrolador el archivo de código fuente de su preferencia (`code sensor 5s - 80cm` o `code sensor 5s - 20s`) según los parámetros de rango y persistencia temporal requeridos en su aplicación.

---
<p align="center">Desarrollado bajo estándares avanzados de ingeniería ciberfísica e IoT.</p>
