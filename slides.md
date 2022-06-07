---
# try also 'default' to start simple
theme: apple-basic
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# some information about the slides, markdown enabled
info: |
  ## Introducción al equipo de TT&T
layout: intro-image
image: https://images.unsplash.com/photo-1517976547714-720226b864c1?w=3456
colorSchema: 'light'
fonts:
  sans: "Inter"
  serif: "Inter"
  mono: "Fira Code"
---

<div class="absolute top-10">
  <span class="font-700">
    Estudiantes por el Desarollo Aeroespacial
  </span>
</div>

<div class="absolute bottom-10">
  <h1>Introducción a la telemetría, rastreo y control</h1>
</div>

---

# Coordinadores del Equipo
### Antonia Merdanevic
Foto y bio
### Agustín Covarrubias
Foto y bio


---
layout: image-right
image: icono_ttyt.png
---

# ¿Qué es la telemetría, rastreo y control?

- Un equipo interdisciplinario que abarca **la microelectrónica, la programación, el modelamiento matemático y el cómputo numérico**.
- Nuestros objetivos son poder lanzar el cohete, monitorear su estado en todo momento, controlarlo en vuelo y (eventualmente) asegurar su aterrizaje.


---
layout: section
---

# Telemetría 📡

---
layout: image-right
image: openmct2.png
---
# Telemetría 📡

- Transmisión remota de mediciones de temperatura, presión, posición, estado, etc.
- Permite monitorear misión remotamente, en distintas fases (lanzamiento, despliegue, aterrizaje).
- Necesita cumplir dos criterios: **integridad** (no tener defectos o errores de transmisión) y **disponibilidad** (no caerse o interrumpirse).
- Necesita haber una forma eficiente de visualizar los datos: proyectos como [OpenMCT](https://nasa.github.io/openmct/) de NASA (a la derecha).

<!-- <img alt="Panel de OpenMCT" src="openmct.png" style='height: 60%; width: 60%; object-fit: contain'> -->

---
layout: image-right
image: https://cdn.cnn.com/cnnnext/dam/assets/141029083137-01-antares-explosion.jpg
---

# Seguridad a Distancia (Range Safety)

- Es el estado de seguridad frente a accidentes catástroficos de una vehículo de lanzamiento.
- Utiliza los de sistemas de terminación de vuelo (FTS), tanto remotos como autónomos.
- Requiere el uso de telemetría confiable para determinar el estado del cohete y si se cumplen los criterios de terminación (FTC).


---
layout: image-right
image: telemetry.png
---

# Comunicaciones

- Enviar y recibir información **digital** usando antenas.
- Muchos protocolos distintos de comunicación, que operan en distintas frecuencias.
- Requiere considerar rango (> 5 km), latencia (RTT), tasa de transferencia de datos y tasa de errores.
- Usamos el protocolo [LoRa (Long Range)](https://en.wikipedia.org/wiki/LoRa), pero no está fijo.
- Adicionalmente requiere un formato de intercambio de datos (nosotros usamos [Protocol Buffers](https://developers.google.com/protocol-buffers) de Google).

---
layout: section
---

# Integración y rastreo 🌎

---
layout: image-right
image: kalman.png
---

# Integración y rastreo

- A veces necesitamos procesar e integrar los datos de los distintos sensores.
- Combinar distintos sensores para mejorar la presición de una medición $\rightarrow$ **[sensor fusion](https://en.wikipedia.org/wiki/Sensor_fusion)**.
- Nos permite aprovechar **[redundancia](https://en.wikipedia.org/wiki/Redundancy_(information_theory))** en las fuentes de información.
- Se usan técnicas de **[estimación de estado](http://library.lol/main/C65011DCD96E6063462D51250049A2B6)**, como [Filtros de Kalman](https://en.wikipedia.org/wiki/Kalman_filter) para intentar determinar el estado del cohete.

---
layout: section
---

# Control ($\Delta$)

---
layout: image-right
image: vectoring.png
---

# Control

- El objetivo es controlar los actuadores en el cohete para conseguir que este llegue a un estado deseado.
- Los actuadores pueden ser válvulas de propulsión, gimbales o incluso un paracaídas.
- El controlador de vuelo recibe información de los sensores y lo usa para calcular correcciones de estado ($\Delta$).
- Es el objeto de estudio de la **[teoría de control](https://www.cds.caltech.edu/~murray/books/AM08/pdf/fbs-public_24Jul2020.pdf)** y se modela matemáticamente como sistemas y bucles de retroalimentación.

---

# Bucle de Control (cerrado)
<img src="bucle.png" alt="Bucle de Control" style='height: 100%; width: 100%; object-fit: contain'>

---
layout: section
---

# Subsistema de TT&C

---
layout: image-right
image: ava-flight-computer.png
---

# Subsistema de TT&C

- Es el subsistema del cohete que se encarga de las tareas de telemetría, rastreo y control.

- Las tareas pueden ocurrir en distintos computadores (microcontroladores) coordinados por un controlador principal, o en solo un computador.

- Requiere hacer uso de **programación embebida** debido a restricciones de recursos, latencia y predictabilidad.

---
layout: center
---

# Componentes de nuestro subsistema

<img src="componentes-computador.png" style='height: 100%; width: 100%; object-fit: contain'>

---
layout: image-right
image: https://content.wolfram.com/uploads/sites/39/2017/04/RaspberryPi-SenseHAT.gif
---

# Programación embebida

- Debemos preocuparnos de muchos detalles de bajo nivel, como la memoria disponible, los pines a los que conectarse, el reloj del controlador, etc.
- Requiere trabajar en computadores relativamente limitados en recursos (CPU, RAM, memoria, etc)
- Se usan lenguajes como C, C++, Ada y Rust, en entornos como [Arduino](https://www.arduino.cc/) o [PlatformIO](https://platformio.org/) (lo que usamos actualmente).
- Foco de los primeros talleres: C y C++.

---

# Ejemplo de código (`blink`)
```rust
#![no_std]
#![no_main]

use panic_halt as _;

#[arduino_hal::entry]
fn main() -> ! {
    let dp = arduino_hal::Peripherals::take().unwrap();
    let pins = arduino_hal::pins!(dp);

    // El pin 13 es el LED incorporado
    let mut led = pins.d13.into_output();
    led.set_high();

    loop {
        led.toggle();
        arduino_hal::delay_ms(100);
        led.toggle();
        arduino_hal::delay_ms(200);
    }
}
```

---

# ¿Qué sigue? 📈

- Tendremos talleres las próximas cinco semanas, primero sobre programación y luego sobre microcontroladores y comunicación.
- Cada semana habrá una reunión, semana por medio será una reunión de este equipo, semana por medio será de todo EDAE.
- Terminado los talleres, tendremos que iniciar la fase de diseño, decidiendo los componentes que vamos a usar y qué funcionalidad vamos a implementar.
- Tendremos que definir encargades de distintas líneas de trabajo, como comunicaciones, telemetría, modelamiento, control, etc.
