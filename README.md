## 🛰️ 1. Introducción

Este proyecto implementa un sistema completo de telemetría en tiempo real para un carro RC, integrando sensores, comunicación inalámbrica nRF24L01, un backend en Python y una interfaz interactiva creada en Lovable.

El sistema permite monitorear variables esenciales durante competencia o pruebas técnicas, tales como:

* Nivel de batería

* Temperatura del motor

* Posición GPS y velocidad

* Acelerómetro y giroscopio (IMU)

* Señales PWM del servo y motor

* Sensor TCRT5000 (detección de meta)

* Tiempos de vuelta

Estado del nodo y timestamp

Este informe describe la arquitectura, el diseño, la metodología de desarrollo y el funcionamiento de cada componente.

## 🧱 2. Arquitectura del Sistema

El sistema se divide en cinco componentes principales:

🕹️ Control Remoto (TX)
     └── Comandos de dirección/aceleración por RF

🚗 Carro RC (TX Telemetría)
     └── Sensores → RF binario → RX

📡 Estación de Telemetría (RX)
     └── Lectura RF → USB Serial

🖥️ Backend WebSocket (PC)
     └── Python / asyncio
     └── Serial → WebSocket (10 Hz)

📊 Dashboard Lovable (Frontend)
     └── Visualización en tiempo real

## 🛠️ 3. Metodología
* A. Construcción del Carro RC (Mecánica y Eléctrica)

Integración estructural del chasis

Instalación del motor, servo de dirección y caja portabaterías

Cableado y distribución de energía

Montaje de sensores: GPS, IMU, TCRT5000, temperatura

Aislamiento electromagnético para evitar ruido sobre la IMU

* B. Implementación del Control Remoto (TX)

Raspberry Pi Pico W como unidad emisora

Dos joysticks analógicos para dirección y aceleración

Botón de parada de emergencia

Comunicación RF nRF24L01 con protocolo binario de baja latencia

Envío de comandos a 15–20 Hz

* C. Implementación del Carro (TX de Telemetría)

Incluye:

Lectura de sensores reales

ADC para tensión de batería

Lectura IMU MPU6050 vía I2C

GPS NEO-6M vía UART

Sensor óptico TCRT5000

Generación de paquete binario optimizado (32 bytes)

Codificación IEEE754 y enteros de 16 bits

Envío por nRF24L01

Canal RF: 2476 MHz (canal 76)

Frecuencia de envío: 10 Hz

* D. Estación de Telemetría (RX)

Raspberry Pi Pico 2W como receptor dedicado

nRF24L01 configurado en recepción continua

Decodificación de tramas binarias

Conversión a JSON

Envío por USB Serial hacia el PC

* E. Backend WebSocket (PC – Python)

Funciona como puente:

Pico RX (Serial USB) → WebSocket Server → Dashboard Lovable


* Características:

Servidor WebSocket en ws://localhost:8888

Procesamiento de datos en tiempo real

Validación y limpieza del JSON

Rate limit 10 Hz para no saturar el frontend

Broadcast a múltiples clientes simultáneos

Se emplea asyncio + websockets para alto rendimiento.

* F. Interfaz Web en Lovable (Dashboard)

La interfaz incluye:

Vista general del sistema

Mapa GPS en tiempo real

Gráficas IMU (aceleración y giro)

Indicador de batería y temperatura

Panel PWM

Cronómetro y contador de vueltas

Estado del sensor de meta

Timestamp del último paquete recibido

Se conecta automáticamente al backend mediante:

const socket = new WebSocket("ws://localhost:8888");

## 📊 4. Resultados

* Prueba del motor y servo via NRF24L01 exitosa
* Aceleración, Neutro y desaceleración del motor con el ESC exitosa
* Implementación de los joystick tano para motor y servo exitosa 

## 🧪 5. Pruebas Realizadas

* Prueba de campo con movimiento real

* Pruebas de ruido IMU

* Pruebas RF en distancias 5–40 m

* Simulación sin sensores reales
