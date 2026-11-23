<div align="center">

# ESP32 HUB75 Clock Studio

Reloj inteligente y estación meteorológica de alta fluidez para paneles LED RGB **64×64 HUB75**, impulsado por **ESP32** con renderizado paralelo en DRAM a **120 FPS / 200 Hz**, degradados de 16 bits, efectos *smoked-glass* y estudio de configuración web integrado.

![Version](https://img.shields.io/badge/version-v1.0.30-0ea5e9?style=flat-square)
![Platform](https://img.shields.io/badge/platform-ESP32-334155?style=flat-square)
![Matrix](https://img.shields.io/badge/HUB75-64x64_1%2F32_scan-1e293b?style=flat-square)
![Engine](https://img.shields.io/badge/render-120_FPS_%2F_200_Hz-0284c7?style=flat-square)
[![Web Studio](https://img.shields.io/badge/Web_Studio-Simulador_en_vivo-2563eb?style=flat-square)](https://alexgremory255.github.io/esp32customclock/)
[![USB Flasher](https://img.shields.io/badge/Instalar-Flashear_por_USB-059669?style=flat-square)](https://alexgremory255.github.io/esp32customclock/#flash)

</div>

---

## Vista previa

<div align="center">
  <a href="https://alexgremory255.github.io/esp32customclock/">
    <img src="docs/preview.gif" alt="Vista previa animada del panel LED 64x64 HUB75" width="380">
  </a>
  <p>
    <sub>GitHub bloquea la ejecución de scripts <code>&lt;canvas&gt;</code> dentro de archivos Markdown; haz clic en el panel superior para abrir el <strong>Web Studio interactivo a 120 Hz</strong> y probar las 20 carátulas, 21 fondos animados y 41 paletas de color en tiempo real.</sub>
  </p>
</div>

---

## Características principales

- **Motor gráfico a 120 FPS / 200 Hz:** Bus I2S-DMA a `16 MHz` sobre búfer doble y composición en DRAM (`canvasBuf`) con trigonometría interpolada de 16 bits (`65,536` ángulos por revolución) y tramado espacial Bayer 4×4 anti-bandeo.
- **Barrido sub-píxel de segundero:** Movimiento continuo tipo *Spring Drive* (`15,360` pasos por minuto mediante muestreo `gettimeofday()`) con *anti-aliasing* en punta y bordes.
- **Diseño modular:** 20 carátulas seleccionables (10 en orientación horizontal y 10 en orientación vertical) además de modo pantalla completa.
- **Fondos, color y acabados:** 21 efectos animados procedurales, 41 paletas de color (sólidas, duales, triples e iridiscentes), cápsulas *smoked-glass* y tipografía con efecto *frosted glass*.
- **Clima en vivo:** Integración con OpenWeatherMap (temperatura actual, íconos y pronóstico próximo a 3 intervalos) y sincronización horaria NTP.
- **Web Studio y Cloud OTA:** Interfaz web embebida (comprimida con gzip en Flash) + portal externo en GitHub Pages con descubrimiento en red local y actualización OTA en un clic.
- **Instalador USB en navegador:** Flasheo completo desde cero por WebSerial sin instalar compiladores ni drivers adicionales.

---

## Requisitos de hardware

Compatibilidad equivalente a **Clockwise / ClockWise Plus**:

| Componente | Especificación recomendada | Notas |
| :--- | :--- | :--- |
| **Panel LED** | Matriz RGB **64×64** con interfaz **HUB75 / HUB75E** | Escaneo **1/32** (requiere pin `E`). Pitch **P2.5** o **P3** recomendado para escritorio o pared. |
| **Microcontrolador** | **ESP32 DevKit** (`ESP32-WROOM` o `ESP32-WROVER`) | Incompatible con ESP8266; utiliza periférico I2S DMA paralelo de ESP32 clásico. |
| **Fuente de poder** | **5V DC regulada, 3A – 4A** (mínimo) | Un panel 64×64 consume corriente elevada en blanco o brillo alto. |
| **PCB opcional** | **WiseShield-32** (pinout estándar Clockwise) | Permite montar el ESP32 directamente sobre el conector HUB75 sin cables Dupont. |

> **Advertencia de alimentación:** No alimentes el panel LED 64×64 a través del pin `5V` ni del puerto USB del ESP32. Conecta la fuente de 5V/3A–4A directamente a las terminales de potencia del panel y alimenta el ESP32 desde la misma fuente regulada (pin `VIN`/`5V` o bifurcación en paralelo), asegurando **tierra (`GND`) común** entre fuente, panel y ESP32.

---

## Conexiones (Pinout HUB75)

Este proyecto utiliza el mapa de pines estándar de **Clockwise / WiseShield-32** definido en [`src/config.h`](src/config.h):

| Pin HUB75 | GPIO ESP32 | Función | Pin HUB75 | GPIO ESP32 | Función |
| :--- | :---: | :--- | :--- | :---: | :--- |
| **R1** | `25` | Dato Rojo (mitad superior) | **A** | `23` | Dirección de fila A |
| **G1** | `26` | Dato Verde (mitad superior) | **B** | `19` | Dirección de fila B |
| **B1** | `27` | Dato Azul (mitad superior) | **C** | `5` | Dirección de fila C |
| **R2** | `14` | Dato Rojo (mitad inferior) | **D** | `17` | Dirección de fila D |
| **G2** | `12` | Dato Verde (mitad inferior) | **E** | `18` | Dirección de fila E (scan 1/32) |
| **B2** | `13` | Dato Azul (mitad inferior) | **CLK** | `16` | Reloj de desplazamiento |
| **LAT** | `4` | Latch de fila | **OE** | `15` | Output Enable (brillo PWM) |
| **GND** | `GND` | Tierra común obligatoria | | | |

---

## Instalación rápida (ESP32 nuevo por USB)

Si tienes un ESP32 sin configurar y quieres cargar el firmware completo (`bootloader`, tabla de particiones, `boot_app0` y aplicación) directamente desde el navegador:

1. Conecta el ESP32 a tu computadora por USB y abre **[alexgremory255.github.io/esp32customclock/#flash](https://alexgremory255.github.io/esp32customclock/#flash)** en **Google Chrome** o **Microsoft Edge**.
2. Haz clic en **Flashear USB**, selecciona el puerto serie (CP2102 / CH340) y espera a que termine la carga. *(Si no inicia la escritura automáticamente, mantén presionado el botón `BOOT` al conectar).*
3. Al arrancar por primera vez, el reloj creará la red Wi-Fi abierta **`ESP32-Clock`**. Conéctate desde tu teléfono o PC para ingresar los datos de tu red Wi-Fi, ciudad y zona horaria.
4. Una vez conectado a tu red local, abre la IP mostrada al inicio (o `http://esp32clock.local`) o usa el **Web Studio** para personalizar diseño, fondo, paleta y brillo.

> Mantener presionado el botón **`BOOT` (`GPIO 0`)** por 3 segundos al encender borra la configuración guardada en NVS; una pulsación corta con el reloj encendido muestra en pantalla la IP local y el tiempo activo.

---

## Compilación local (PlatformIO)

Dependencias declaradas en [`platformio.ini`](platformio.ini):
- `mrfaptastic/ESP32 HUB75 LED MATRIX PANEL DMA Display @ 3.0.15`
- `adafruit/Adafruit GFX Library @ 1.11.11`
- `bblanchon/ArduinoJson @ 6.21.3`

```bash
# Compilar y subir por USB (entorno usb)
pio run -e usb -t upload

# Abrir monitor serie (115200 baudios)
pio device monitor -b 115200
```

La UI embebida ([`index.html`](index.html)) se comprime automáticamente en [`src/web_ui_gz.h`](src/web_ui_gz.h) antes de compilar mediante [`publicar_ota.py`](publicar_ota.py).
