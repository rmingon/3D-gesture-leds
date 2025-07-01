# 3D Gesture‑Controlled LED Ring

A circular LED display driven by an STM32F103 microcontroller and an MGC3130 3D gesture sensor to control WS2812 (NeoPixel) LEDs via hand movements. The project includes KiCad source files, 3D renders, and Gerber exports for easy fabrication.

---

## Features

- **3D Gesture Control**: Leverage the Microchip MGC3130 to detect hand gestures (swipe, wave, hover) and translate them into LED patterns.
- **STM32F103 MCU**: Cortex‑M3 core running at 72 MHz, handling USB, I²C, and the WS2812 timing-critical data stream.
- **LED Ring**: 16 WS2812B individually addressable RGB LEDs arranged in a circular pattern for smooth animations.
- **Compact PCB**: Single‑layer circular board with ground‑plane perforations for weight reduction and better RF performance.
- **Extensible Firmware**: Modular code written in STM32CubeIDE (HAL) that can be adapted to other sensors or LED configurations.

---

## Bill of Materials (BOM)

| Qty | Ref Des | Part               | Value   | Package  |
| --- | ------- | ------------------ | ------- | -------- |
| 1   | U1      | MGC3130‑I\_MQ      | —       | QFN‑28   |
| 1   | U2      | STM32F103C8T6      | —       | LQFP‑48  |
| 16  | D1–D16  | WS2812B            | RGB LED | 5050 SMD |
| 6   | C5–C10  | Ceramic Capacitor  | 2.2 µF  | 0402     |
| 4   | C1–C4   | Ceramic Capacitor  | 100 nF  | 0402     |
| 6   | R1–R10  | Resistor           | 1 kΩ    | 0402     |
| 1   | Conn    | SWD Header         | 4‑pin   | 2.54 mm  |
| 1   | J1      | Programming Header | 5‑pin   | 2.54 mm  |

---

## Board Files

- **KiCad Schematic**: [`hardware.kicad_sch`](./hardware/hardware.kicad_sch)
- **PCB Layout**: [`hardware.kicad_pcb`](./hardware/hardware.kicad_pcb)
- **3D Views**: ![`3d_top.png`](./docs/3d_top.png), ![`3d_bottom.png`](./docs/3d_bottom.png)

---

## Schematic Overview



- **Power**: 3.3 V regulated rail supplies both the STM32 and MGC3130. Bypass and decoupling capacitors placed close to power pins.
- **Gesture Sensor**: MGC3130 communicates over I²C (SDA/SCL) and outputs interrupts for processed gestures.
- **LED Driver**: STM32 PA0 configured as WS2812 data output (single-wire serial at 800 kHz). External resistors ensure clean signal transitions.
- **Debug & Programming**: Standard SWD interface on PA13/PA14 plus UART TX/RX broken out for serial logging.

---

## PCB & Mechanical

- **Circular Form Factor**: Diameter 100 mm, six radial slots for mechanical mounting or light diffusion.
- **Ground Plane Perforation**: 2×2 mm grid removed in non-critical areas to reduce weight and improve air flow.

---

## Firmware

1. **Development Environment**: [STM32CubeIDE]
2. **HAL Libraries**: I²C, GPIO, TIM (for precise delays), and USB (optional debug).
3. **Gesture Handling**:
   - Initialize MGC3130, poll for data-ready interrupts.
   - Read gesture packets over I²C, parse swipe (left/right/up/down) and circle (clockwise/anticlockwise).
   - Map gestures to LED animation functions (e.g., rotate colors, rainbow, blink).
4. **WS2812 Driver**:
   - Use a bit-banged routine with `HAL_TIM_PWM` and DMA to generate the timing waveform.
   - Buffer 16 × 24 bits (GRB) and transmit at 800 kHz.

```c
// Example: Set LED color
void ws2812_set(uint8_t index, uint8_t r, uint8_t g, uint8_t b);
// Example: Rotate animation
void animate_rotate(uint16_t delay_ms);
```

---

## Assembly & Testing

1. **PCB Fabrication**: Send Gerbers to your favorite manufacturer with 0.8 oz copper, HASL finish.
2. **Component Placement**: Follow the silkscreen and reference designators. Solder U1 and U2 with paste reflow or hand soldering.
3. **Firmware Flashing**: Connect SWD header to ST‑Link. Build and flash the `gesture_led_ring` project in STM32CubeIDE.
4. **Power‑On Test**:
   - LEDs should initially perform a startup sequence.
   - Hover your hand 5 cm above the sensor: LEDs will light up.
   - Swipe left/right: ring rotates colors accordingly.

---

## License

Hardware is released under CERN-OHL-S v2.
Firmware samples are MIT.
See individual source files for details.

---

*Feel free to fork, adapt, and contribute!*

