# tfg-telemetria-deportiva
Prototipo de telemetría biométrica y geolocalización de bajo coste (ESP32 + MQTT + InfluxDB) para TFG de Ingeniería Informática (UOC).

Repositorio oficial del Trabajo Final de Grado (TFG) del Grado en Ingeniería Informática (Universitat Oberta de Catalunya - UOC).

**Autor:** Francisco Javier Reyes Ureña 
**Director:** Emanuel Ortiz Ortiz 
**Área:** Sistemas empotrados

## Descripción
Sistema modular y de código abierto basado en **ESP32** para la monitorización en tiempo próximo al real de frecuencia cardíaca, temperatura superficial y posición GPS durante pruebas de resistencia deportiva. Los datos se transmiten en formato JSON mediante **MQTT** sobre Wi-Fi hacia un backend contenerizado con **Docker** (Mosquitto, Telegraf e InfluxDB).

> **Aviso:** Este prototipo tiene fines estrictamente académicos y de validación técnica. No es un dispositivo médico ni tiene finalidad diagnóstica.

---
## Estructura del repositorio
```text
├── docs/
│   └── protocolo_pruebas.md    # Protocolo de validación experimental y seguridad
├── firmware/
│   ├── pruebas_individuales/   # Códigos de validación aislada (MAX30102, MCP9808, GPS)
│   └── telemetria_esp32/       # Firmware integrado final (adquisición + MQTT a 1 Hz)
└── backend/
    ├── docker-compose.yml      # Orquestación de servicios
    ├── .env.example            # Plantilla de variables de entorno (sin credenciales)
    ├── mosquitto/config/       # Configuración del broker MQTT
    └── telegraf/               # Configuración de enrutamiento MQTT -> InfluxDB
```

## Hardware y conexionado

| Componente | Modelo | Interfaz / Pines ESP32 | Detalle |
| :--- | :--- | :--- | :--- |
| **Microcontrolador** | ESP32 LOLIN32 Lite | — | Wi-Fi / BLE integrado |
| **Pulso (PPG)** | MAX30102 | I2C (`SDA: 23`, `SCL: 22`) | Dirección `0x57` |
| **Temperatura** | MCP9808 | I2C (`SDA: 23`, `SCL: 22`) | Dirección `0x18` |
| **GPS** | GY-NEO6MV2 | UART2 (`RX: 16`, `TX: 17`) | 9600 bps |
| **Alimentación** | LiPo 3,7 V 1800 mAh | Conector JST-PH | 6,7 Wh (sustituye a la inicial de 500 mAh) |

---

## Versiones y dependencias

### Firmware (Arduino IDE / C++)
* **Placa:** ESP32 (`esp32 by Espressif Systems`) `v3.3.12`
* **Librerías:**
  * `SparkFun MAX3010x` `v1.1.2`
  * `Adafruit MCP9808` `v2.0.2`
  * `TinyGPSPlus` `v1.0.3`
  * `PubSubClient` `v2.8`

### Backend (Docker)
* **Docker / Docker Compose:** `v29.9.3`
* **Eclipse Mosquitto:** `2.0`
* **Telegraf:** `1.28`
* **InfluxDB:** `2.7`

---

## Cómo reproducir el entorno

### 1. Despliegue del backend
1. Accede a la carpeta del servidor:
   ```bash
   cd backend
   ```
2. Crea tu archivo de variables de entorno a partir de la plantilla y rellena tus propias credenciales (token de InfluxDB y usuario/contraseña de Mosquitto):
   ```bash
   cp .env.example .env
   ```
3. Levanta los contenedores:
   ```bash
   docker compose up -d
   ```
4. Verifica que los tres servicios (`mosquitto`, `telegraf`, `influxdb`) están activos con `docker compose ps`.

### 2. Carga del firmware en el ESP32
1. Abre el proyecto en `firmware/telemetria_esp32/` con Arduino IDE.
2. Instala las librerías indicadas en la sección de dependencias.
3. Crea el archivo `secrets.h` a partir de `secrets.h.example` con el SSID/contraseña de tu punto de acceso Wi-Fi y la IP/credenciales de tu broker Mosquitto.
4. Compila y sube el código a la placa **LOLIN32 Lite**.

---

## Licencia
El código fuente de este repositorio se distribuye bajo licencia **MIT**. La memoria del TFG está sujeta a licencia Creative Commons BY-NC 3.0.
