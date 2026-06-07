# 🚌 SMART-TRANSIT-HUB-SYSTEM

> A fully embedded, solar-powered smart bus stop system using ESP32, ESP32-C3 SuperMini, NEO-6M GPS, PAM8403 Audio Amplifier, 3.5" Touch TFT Display, and Micro SD Card — with real-time GPS cloud tracking, conductor-triggered fault announcements, touch-based route search, and autonomous solar-battery power management via ThingSpeak IoT integration.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [System Architecture](#system-architecture)
- [Block Diagram](#block-diagram)
- [Hardware Components](#hardware-components)
- [Software Tools](#software-tools)
- [Circuit & Pin Connections](#circuit--pin-connections)
- [How It Works](#how-it-works)
- [Results](#results)
- [Future Scope](#future-scope)

---

## Overview

Public bus transportation networks across urban and semi-urban regions suffer from persistent inadequacies in real-time passenger information delivery, proactive fault communication, and efficient energy management at static bus stop infrastructure. Conventional bus stops provide no mechanism for informing waiting passengers about the current location of approaching buses, the estimated time of arrival, or the operational status of vehicles on a particular route.

This project presents a **fully embedded, solar-powered IoT Smart Bus Stop with Real-Time GPS Tracking, Fault Announcement, and Passenger Information System**. The system is organized around a distributed two-unit architecture — a **bus-side mobile unit** built around an **ESP32-C3 SuperMini** connected to a **NEO-6M GPS module** and two conductor-operated trigger switches, and a **bus stop fixed unit** centered on an **ESP32 master controller** driving a **3.5-inch SPI Touch TFT Display**, **PAM8403 audio amplifier**, and **Micro SD TF Card Reader** for pre-recorded audio announcements. GPS coordinates are continuously uploaded to the **ThingSpeak IoT cloud platform** for live map tracking, while bus-to-stop fault notifications are delivered over a direct Wi-Fi access point and station link — entirely independent of any external router or cellular infrastructure at the stop. The bus stop unit is powered autonomously by **dual 6V solar panels**, a **lithium-ion battery**, and a **1S USB Type-C BMS** protection circuit.

---

## Features

- ✅ Distributed two-unit architecture — ESP32-C3 bus-side mobile unit and ESP32 bus stop fixed unit
- ✅ Real-time GPS bus location tracking using NEO-6M module with ThingSpeak cloud map visualization
- ✅ Conductor-triggered fault and fault-rectification audio announcements transmitted over Wi-Fi 
- ✅ Interactive 3.5-inch SPI Touch TFT display with graphical user interface
- ✅ Pre-recorded natural language audio announcements played through PAM8403 amplifier from SD storage
- ✅ Dual-core FreeRTOS task architecture on ESP32 for Wi-Fi fault listener, display and audio management
- ✅ TinyGPS++ NMEA sentence parsing for validated GPS coordinate extraction on ESP32-C3
- ✅ ThingSpeak Map Widget with live bus position marker updating at 15-second intervals
- ✅ Direct ESP32 access point and ESP32-C3 station topology — no external router dependency
- ✅ Solar-powered operation — dual 6V polycrystalline panels, 18650 lithium-ion battery, 1S USB Type-C BMS
- ✅ Battery voltage monitoring via ESP32 ADC with charge status displayed on TFT interface
- ✅ Low-cost, compact, and suitable for student research and municipal transit prototyping

---

## System Architecture

The system is divided into three functional layers:

```
┌─────────────────────────────────────────────────────────────────────┐
│  BUS-SIDE UNIT       NEO-6M GPS Module (Location Acquisition)       │
│                      Conductor Trigger Switch 1 (Fault Report)      │
│                      Conductor Trigger Switch 2 (Fault Resolved)    │
├─────────────────────────────────────────────────────────────────────┤
│  COMMUNICATION       ESP32-C3 SuperMini (RISC-V @ 160 MHz)          │
│  LAYER               TinyGPS++ NMEA Parsing, ThingSpeak HTTP Upload │
│                      Wi-Fi Station → ESP32 Access Point (Fault TX)  │
├─────────────────────────────────────────────────────────────────────┤
│  BUS STOP UNIT       ESP32 (Dual-Core Xtensa LX6 @ 240 MHz)         │
│                      3.5" Touch TFT Display (SPI), PAM8403 + Speaker│
│                      Micro SD Card Reader (SPI), Solar Power System │
└─────────────────────────────────────────────────────────────────────┘
```

### Block Diagram

```
NEO-6M GPS ──┐
Switch 1    ──►  ESP32-C3 SuperMini (Bus Unit) ──► Wi-Fi ──► ESP32 (Bus Stop) ──► 3.5" Touch TFT (SPI)
Switch 2    ──┘   │  TinyGPS++ Parsing                          │              PAM8403 Audio Amplifier
                  │  ThingSpeak HTTP Upload                      │              Micro SD Card (Audio Files)
                  │                                              │
                  ▼                                              ▼
          ThingSpeak Cloud Platform                    Solar Power Subsystem
          └── GPS Map Widget (Live)                   └── Dual 6V Solar Panels
                                                      └── 1S USB Type-C BMS
                                                      └── 18650 Li-Ion Battery
```

---

## Hardware Components

| Component | Model | Interface | Purpose |
|---|---|---|---|
| Bus Stop Controller | ESP32 (Dual-Core Xtensa LX6) | — | Central orchestration — display, audio, Wi-Fi AP, touch management |
| Bus-Side Controller | ESP32-C3 SuperMini (RISC-V) | — | GPS acquisition, ThingSpeak upload, fault Wi-Fi transmission |
| GPS Module | NEO-6M (u-blox 6 Engine) | UART (9600 baud) | Real-time bus geographic coordinate acquisition |
| Touch TFT Display | 3.5" ILI9488, 480×320, XPT2046 touch | SPI | Graphical route map, bus search interface, fault status display |
| Audio Amplifier | PAM8403 Class-D Stereo, 1.5W @ 8Ω | DAC (GPIO 25) | Driving 8-ohm speaker for audio announcements |
| SD Card Reader | Micro SD TF Card Reader Module | SPI | Pre-recorded WAV/MP3 audio file storage and retrieval |
| Solar Panel | 6V Polycrystalline Silicon × 2 | — | Daytime solar energy harvesting for bus stop |
| Battery Management | 1S USB Type-C BMS | — | Li-ion charging, overvoltage, undervoltage, overcurrent protection |
| Battery | 18650 Lithium-Ion Cell (3.7V) | — | Energy storage for overnight and low-irradiance operation |
| Trigger Switches | Conductor Push Switches × 2 | GPIO (ESP32-C3) | Fault occurrence and fault rectification signaling |

---

## Software Tools

| Tool | Purpose |
|---|---|
| **Arduino IDE** | Primary firmware development, compilation, and upload environment for both ESP32 and ESP32-C3 units |
| **Espressif Arduino-ESP32 BSP** | Board support package — toolchain, bootloader, peripheral drivers, and serial upload for ESP32 family |
| **TFT_eSPI Library (Bodmer)** | Optimized SPI display driver for ILI9488 with hardware DMA support for fast screen updates |
| **XPT2046 Library** | Resistive touchscreen coordinate reading and calibration over SPI bus |
| **TinyGPS++ Library** | NMEA sentence parsing engine with checksum validation for NEO-6M GPS data extraction |
| **SD Library (ESP32 BSP)** | FAT16/FAT32 filesystem access for audio file retrieval from Micro SD card |
| **WiFi Library (ESP32 BSP)** | Wi-Fi access point and station management, HTTP client for ThingSpeak uploads |
| **FreeRTOS (Integrated)** | Dual-core task scheduling — Wi-Fi listener on Core 0, passenger interface on Core 1 |
| **ThingSpeak IoT Platform** | Cloud dashboard with GPS Map Widget for live bus position visualization |

---

## Circuit & Pin Connections

### ESP32-C3 SuperMini — Bus-Side Unit (GPS + Fault Signaling)

| GPIO Pin | Function | Connected Peripheral | Description |
|---|---|---|---|
| GPIO 0 | UART RX | NEO-6M GPS TX | Receives NMEA sentence stream at 9600 baud |
| GPIO 1 | UART TX | Serial Debug Output | Serial monitoring during development |
| GPIO 3 | Switch 1 IN | Fault Occurrence Trigger | Falling edge interrupt — generates fault Wi-Fi packet |
| GPIO 4 | Switch 2 IN | Fault Rectification Trigger | Falling edge interrupt — generates fault-resolved Wi-Fi packet |
| GPIO 8 | Status LED | System Activity Indicator | Visual firmware activity indication |

### ESP32 — Bus Stop Unit (Display, Audio, Power)

| GPIO Pin | Function | Connected Peripheral | Description |
|---|---|---|---|
| GPIO 5 | TFT CS | TFT Display Chip Select | SPI chip select for ILI9488 display driver |
| GPIO 4 | TFT DC | TFT Data/Command | Data or command register write selection |
| GPIO 22 | TFT RST | TFT Reset | Hardware reset for display driver IC |
| GPIO 18 | SPI SCK | TFT + SD Card Clock | Shared SPI clock for display and SD card |
| GPIO 23 | SPI MOSI | TFT + SD Card Data In | Shared SPI data output to display and SD card |
| GPIO 19 | SPI MISO | SD Card Data Out | SPI data from SD card to ESP32 |
| GPIO 15 | SD CS | Micro SD Card Chip Select | SPI chip select for SD card (separate from TFT CS) |
| GPIO 25 | DAC OUT | PAM8403 Audio Input | Analog audio signal from ESP32 DAC to amplifier |
| GPIO 2 | Touch IRQ | TFT Touch Interrupt | Active-low interrupt when passenger touch detected |
| GPIO 21 | PAM SD | PAM8403 Shutdown Control | Active-low shutdown for audio power management |

### NEO-6M GPS Module — Pin Configuration

| Pin | Description | Function |
|---|---|---|
| VCC | Power Supply | Operating voltage: 3.3V or 5V |
| GND | Ground | Common ground reference |
| TX | UART Transmit | Outputs NMEA sentence data to ESP32-C3 RX |
| RX | UART Receive | Receives commands from ESP32-C3 TX |
| PPS | Pulse Per Second | Outputs 1 Hz timing pulse |

### PAM8403 Audio Amplifier — Pin Configuration

| Pin | Description | Function |
|---|---|---|
| VCC | Power Supply | 5V supply for amplifier operation |
| GND | Ground | Common ground reference |
| L_IN | Left Audio Input | Analog audio signal from ESP32 DAC output |
| R_IN | Right Audio Input | Analog audio input (bridged with L_IN for mono) |
| L_OUT+ | Left Output Positive | Differential speaker output positive terminal |
| L_OUT- | Left Output Negative | Differential speaker output negative terminal |
| SD | Shutdown | Active-low shutdown; driven by ESP32 GPIO 21 |

### 3.5-Inch Touch TFT Display — Pin Configuration

| Pin | Description | Function |
|---|---|---|
| VCC | Power Supply | 3.3V supply for display controller |
| GND | Ground | Common ground reference |
| CS | Display Chip Select | SPI chip select for ILI9488 display driver |
| RESET | Display Reset | Hardware reset for display driver IC |
| DC/RS | Data/Command | Selects data or command register write mode |
| MOSI | Master Out Slave In | SPI data to display driver |
| SCK | Serial Clock | SPI clock signal from ESP32 |
| MISO | Master In Slave Out | SPI readback from display |
| T_CS | Touch Chip Select | SPI chip select for XPT2046 touch controller |
| T_IRQ | Touch Interrupt | Active-low interrupt when touch detected |

### 1S USB Type-C BMS — Pin Configuration

| Pin | Description | Function |
|---|---|---|
| USB Type-C IN | Charging Input | Accepts 5V USB or solar input for charging |
| B+ | Battery Positive | Connects to + terminal of Li-ion cell |
| B- | Battery Negative | Connects to − terminal of Li-ion cell |
| OUT+ | Output Positive | Regulated output to ESP32 and load circuit |
| OUT- | Output Negative | Ground reference for load circuit |
| LED (CHG) | Charge Indicator | Red: charging; Blue: charge complete |

### Solar Power Subsystem — Specification

| Parameter | Specification | Notes |
|---|---|---|
| Nominal Voltage | 6V | Open-circuit output per panel |
| Quantity | 2 panels | Series configuration via BMS input |
| Panel Type | Polycrystalline Silicon | Application-specific selection |
| Output Connector | Flying lead with JST | Connects to BMS solar charging input |

---

## How It Works

1. **Boot & Init (Bus Stop ESP32)** — ESP32 initializes the TFT display via SPI using the TFT_eSPI library, mounts the Micro SD card filesystem, configures the Wi-Fi access point, and starts FreeRTOS tasks — Core 0 for Wi-Fi fault listener, Core 1 for passenger interface loop.
2. **Boot & Init (Bus-Side ESP32-C3)** — ESP32-C3 SuperMini initializes UART0 for NEO-6M GPS reception, configures GPIO interrupt inputs for conductor trigger switches with internal pull-up resistors, and connects to the available mobile hotspot for ThingSpeak uploads.
3. **GPS Acquisition** — NEO-6M continuously transmits NMEA 0183 sentences at 9600 baud to the ESP32-C3. The TinyGPS++ library parses GGA and RMC sentences with checksum validation, extracting latitude, longitude, speed, and fix quality parameters.
4. **ThingSpeak Cloud Upload** — ESP32-C3 constructs an HTTP GET request with the validated GPS coordinates — latitude into Field 1, longitude into Field 2 — and transmits to the ThingSpeak REST API endpoint over the mobile hotspot Wi-Fi connection at 15-second intervals.
5. **ThingSpeak Map Visualization** — The ThingSpeak Map Widget on the project channel automatically updates the displayed pin marker position with each new coordinate upload, providing near-real-time bus location tracking accessible from any internet-connected browser or smartphone.
6. **Fault Notification (Bus → Bus Stop)** — When the conductor presses trigger switch 1, a falling-edge GPIO interrupt activates the fault transmission routine on the ESP32-C3, which temporarily connects to the bus stop ESP32 access point, transmits a UDP packet encoding the fault type code, and returns to the mobile hotspot for continued GPS uploads.
7. **Fault Audio Announcement (Bus Stop)** — Upon receiving a fault Wi-Fi packet, the ESP32 Core 0 listener task decodes the fault type, passes the event code to Core 1 via FreeRTOS queue, deasserts the PAM8403 shutdown pin, retrieves the corresponding pre-recorded audio file from the Micro SD card, and plays the announcement through the 8-ohm speaker — all within three seconds of conductor trigger activation.
8. **Fault Rectification** — Pressing trigger switch 2 generates a fault-resolved Wi-Fi packet. On reception, the ESP32 plays the fault-rectification audio announcement and updates the TFT display to reflect normal service status.
9. **Passenger Touch Interface** — The 3.5-inch TFT displays a graphical bus route map with all eight designated stops. When a passenger touches a stop or search element, the XPT2046 touch controller fires an interrupt on GPIO 2; the ESP32 reads the touch coordinates, maps them to the interface element, executes the route availability search algorithm, renders the result table on the TFT, and simultaneously plays the spoken search result audio from the SD card through the PAM8403.
10. **Solar Power Management** — Dual 6V solar panels feed the 1S BMS charging input, which implements constant-current followed by constant-voltage charging to maintain the 18650 lithium-ion cell. The ESP32 ADC periodically samples battery voltage through a voltage divider and displays charge status on the TFT. Validated battery autonomy supports twelve-hour continuous overnight operation from a full daytime charge.

### FreeRTOS Task Architecture

```
ESP32 Core      Task                    Responsibility
─────────────────────────────────────────────────────────
Core 0          Wi-Fi Fault Listener    Access point management, UDP packet reception,
                                        fault event code enqueue via FreeRTOS queue
Core 1          Passenger Interface     TFT display rendering, touch coordinate processing,
                                        bus availability search, SD card audio playback,
                                        battery ADC monitoring, PAM8403 control
```

### ThingSpeak Operation Steps

| Step | Operation | Description |
|---|---|---|
| Step 1 | NMEA Parsing | TinyGPS++ extracts validated latitude/longitude from NEO-6M GGA and RMC sentences |
| Step 2 | Wi-Fi Connection | ESP32-C3 connects to mobile hotspot; constructs HTTP GET with coordinates |
| Step 3 | Data Transmission | HTTP GET to ThingSpeak API — Field 1: Latitude, Field 2: Longitude |
| Step 4 | Map Visualization | ThingSpeak Map Widget updates bus position pin marker; accessible on any browser |

---

## Results

The system was successfully implemented and validated through hardware-level functional tests under laboratory and field conditions representative of actual urban bus service deployment. Key outcomes:

- **GPS Tracking Accuracy** — NEO-6M provided position fixes within the manufacturer-specified 2.5 meters CEP under open sky conditions; ThingSpeak map marker updated consistently within 15–30 seconds of each coordinate upload on both smartphone and laptop browser interfaces.
- **Wi-Fi Communication Range** — Bus-to-stop Wi-Fi link between ESP32-C3 and ESP32 access point validated at distances up to 40 meters in open space, confirming adequate range for bus approach and departure scenarios.
- **Fault Announcement Latency** — Fault audio announcement played through the bus stop speaker within 3 seconds of conductor trigger switch activation; TFT display fault status updated simultaneously.
- **Fault Rectification** — Pressing trigger switch 2 produced the fault-resolved audio announcement and display normalization within the same 3-second response window.
- **Touch Interface** — Passenger stop search queries via the 3.5-inch touch TFT successfully rendered structured result tables with bus numbers, expected arrival times, and operational status; concurrent audio playback confirmed without display flicker.
- **Solar Power Autonomy** — Twelve-hour continuous battery operation from a full charge state validated, confirming adequate energy storage for overnight passenger information service without any grid electrical connection.
- **ThingSpeak Cloud** — Continuous GPS coordinate uploads maintained across the full test session with HTTP 200 acknowledgment confirmed for each upload; map widget position marker updated reliably on both web and mobile interfaces.

---

## Future Scope

- **Extended Route Network** — Deploy additional smart bus stop units along longer route corridors with a multi-hop Wi-Fi relay protocol, where each bus stop ESP32 acts as both receiver and retransmitter toward the next downstream stop.
- **Real-Time ETA Calculation** — Integrate an arrival time estimation function using ThingSpeak-uploaded GPS coordinates and current bus speed to compute and display estimated arrival times at each downstream stop on the TFT interface.
- **Automated Fault Detection** — Add bus-side sensor inputs for engine temperature, oil pressure, and brake condition monitoring, with threshold-crossing events automatically generating structured fault codes transmitted without manual conductor trigger activation.
- **MQTT over TLS** — Replace HTTP GET uploads with MQTT over TLS for encrypted, bidirectional, and lower-latency cloud communication suitable for production transit deployments.
- **Mobile Application** — Develop a dedicated Android / iOS app for real-time bus tracking, stop search, fault status alerts, and push notifications accessible by passengers off-site.
- **Multi-Route Multi-Bus Support** — Extend the system to support multiple bus routes and multiple simultaneous bus units, with ThingSpeak channel fields differentiated per bus and route.
- **Text-to-Speech Synthesis** — Replace pre-recorded SD card audio files with on-device text-to-speech synthesis supporting dynamic stop names and real-time ETA announcements without manual audio file updates.
- **Passenger Crowd Analytics** — Integrate a people-counting sensor at the bus stop to log passenger wait volumes to ThingSpeak, enabling demand-based route frequency optimization by transit operators.
- **Higher-Capacity Solar Array** — Scale the solar subsystem to a larger panel capacity and multi-cell lithium battery pack for deployment in tropical low-irradiance monsoon conditions with extended cloudy-day battery reserve.
- **MODBUS / RS485 Integration** — Add a MODBUS RTU interface for integration with existing municipal SCADA and fleet management infrastructure for large-scale city-wide deployment.

---

> *Built to demonstrate that comprehensive, solar-autonomous smart bus stop infrastructure 
