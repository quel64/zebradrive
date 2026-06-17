# ZebraDrive: Automated Blinds

A low-cost smart blind automation system designed for lazy people. Powered by an ESP32-C3, this project motorizes the existing beaded chain loop, allowing control via a local responsive web application or physical tactile buttons.

## Features
* **Non-Destructive Design:** Pulls the existing beaded chain via an external 3D-printed enclosure—no need to dismantle the window blinds.
* **Silent Operation:** Uses the BigTreeTech TMC2209 driver for silent movement.
* **Safety Limits:** No messy external limit switches. Uses step-counting via the `AccelStepper` library to prevent over-rolling.
* **Heat Idling:** Smartly disables the motor coils completely when resting to save energy and prevent heat buildup.
* **Dual Control Modes:** Local web application interface and tactile hardware buttons for manual overrides.

---

## Bill of Materials (BOM)

| Component | Description | Qty |
| :--- | :--- | :--- |
| **ESP32-C3 Dev Board** | Main microcontroller with Wi-Fi connectivity | 1 |
| **NEMA 17 Stepper Motor** | Standard 40N·cm torque motor with D-shaft | 1 |
| **TMC2209 Stepper Driver** | Silent driver module (e.g., BigTreeTech v1.3) | 1 |
| **12V 2A Power Supply** | Wall adapter with a 2.1mm DC barrel jack | 1 |
| **DC Barrel Jack Adapter** | Screw terminal connector for power routing | 1 |
| **Mini-360 Buck Converter** | DC-DC step-down module (drops 12V down to 5V) | 1 |
| **100µF Capacitor** | Electrolytic capacitor (25V or higher) for driver protection | 1 |
| **Tactile Push Buttons** | Standard 6x6mm momentary buttons for manual control | 2 |
| **Perfboard** | 5x7 cm double-sided prototyping circuit board | 1 |
| **3D Printed Parts** | Enclosure and gear to move the chain loop | 1 |
| **M2/M3/M4 Brass Inserts**| Threaded inserts melted into the 3D print for robust assembly | 1 kit |

---

## Hardware Configuration & Wiring

### 1. Power Distribution
* **12V Source** -> `VMOT` (TMC2209) **&** `IN+` (Mini-360 Buck Converter).
* **GND Source** -> `GND` (TMC2209) **&** `IN-` (Mini-360 Buck Converter).
* **Mini-360 OUT+ (Tuned to 5.0V)** -> `5V / VIN` (ESP32-C3).
* **Mini-360 OUT-** -> `GND` (ESP32-C3).
* Connect the **100µF Capacitor** directly across the TMC2209 `VMOT` and `GND` pins to prevent high-voltage spikes from destroying the driver.

### 2. Logic Wiring
* `GND` (ESP32) -> `GND` (TMC2209 Logic Side)
* `3.3V` (ESP32) -> `VIO` (TMC2209 Logic Power)
* `GPIO 4` (ESP32) -> `STEP` (TMC2209)
* `GPIO 5` (ESP32) -> `DIR` (TMC2209)
* `GPIO 6` (ESP32) -> `EN` (TMC2209 Enable Pin)
* **UP Button:** `GPIO 0` -> Button -> `GND`
* **DOWN Button:** `GPIO 1` -> Button -> `GND`

---

## 3D Printing & Mechanical Assembly

The mechanical housing is split into three main printable components:
1.  **Custom Beaded Sprocket:** Designed to interface directly with the NEMA 17 D-shaft. The outer diameter features custom hemispherical pockets perfectly matching the pitch and diameter of the blind's plastic beads. *(Print with 4-5 walls/perimeters and 40% infill for durability)*.
2.  **Main Enclosure:** Houses the electronics and wraps snugly around the sprocket to act as a chain guide channel, forcing the beads to remain nested inside the gear teeth without slipping.
3.  **Wall Bracket:** Mounted permanently to the wall structure. The main enclosure slides into this bracket to easily adjust chain tension.

---

## Software Setup & Calibration

### Required Libraries
Install the following libraries inside your Arduino IDE Library Manager:
* `AccelStepper`
* `ESPAsyncWebServer`
* `AsyncTCP`

### Calibration Steps
1. Open the project sketch, update the `ssid` and `password` variables with your home Wi-Fi credentials, and flash the code to the ESP32-C3.
2. Manually roll your window blind all the way **Up** to the open position before powering the device. On boot, this position is registered as `0`.
3. Open the Arduino **Serial Monitor** at `115200` baud.
4. Press and hold the physical **Down** button (or use the web app control layout at the displayed local IP) to lower the blinds to the bottom limit.
5. Take the final micro-step count printed on the Serial Monitor, update the `maxClosedPosition` variable at the top of the code, and re-upload the sketch to lock in the boundaries.

## License
This project is open-source and free to modify for personal use.
