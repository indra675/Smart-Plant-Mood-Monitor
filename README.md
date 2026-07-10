# 🌱 Smart Plant Mood Monitor

An IoT-based embedded system built on Arduino UNO that senses soil moisture and ambient light, then translates the readings into simple, human-readable "mood" messages on an OLED display — making plant care intuitive for non-technical users.

Developed as part of the **ESSCI-certified Embedded Systems and IoT Engineering course (ELE/Q1404)** at SRM University AP.

---

## 📌 Overview

Instead of showing raw sensor values, this project maps soil moisture and light intensity readings to four relatable plant "moods":

| Soil Condition | Light Condition | Displayed Mood |
|---|---|---|
| Wet (< 300) | Dark (< 300) | "Cold & Thirsty!" |
| Wet (< 300) | Bright (>= 300) | "Water Me Please!" |
| Dry (>= 300) | Dark (< 300) | "Sleepy zzz..." |
| Dry (>= 300) | Bright (>= 300) | "I Feel Great!" |

---

## 🔧 Hardware Components

| Component | Quantity | Function |
|---|---|---|
| Arduino UNO | 1 | Main microcontroller |
| Soil Moisture Sensor (probe type) | 1 | Measures soil water content (A0) |
| LDR Light Sensor Module | 1 | Measures ambient light (A1) |
| OLED Display (SSD1306, 128x64, I2C) | 1 | Displays mood messages |
| Resistors (200 Ω) | 4 | Current limiting / signal integrity |
| Breadboard | 1 | Prototyping |
| Jumper Wires | Assorted | Connections |
| USB Cable | 1 | Power & programming |

---

## ⚙️ Working Principle

1. Arduino initializes Serial Monitor (9600 baud) and OLED over I2C (address `0x3C`).
2. Each loop cycle reads soil moisture (A0) and light intensity (A1) — both range 0–1023.
3. Values are compared against a fixed threshold of **300** using nested conditional logic.
4. The corresponding mood message is rendered on the OLED via `setMood()`.
5. System waits 2000 ms before repeating.

**Why 300?** The Arduino's 10-bit ADC maps 0–5V to 0–1023. A threshold of ~300 (≈1.47V) was empirically chosen as a reasonable wet/dry and dark/bright dividing line for the sensor batch used. A production version would use a self-calibrating routine instead.

**I2C Bus:** The OLED communicates over I2C (SDA/SCL), needing just two signal lines plus power/ground — simplifying wiring versus a parallel interface.

---

## 🖥️ Circuit Design

Designed and simulated in **Cirkit Designer**, with:
- Soil sensor → A0 (via 200Ω resistor)
- LDR module → A1 (via 200Ω resistor)
- OLED SDA → A4, SCL → A5 (via 200Ω resistors)
- Common ground rail across all modules

*(See `/docs` or the project report PDF for full schematic diagrams.)*

---

## 💻 Firmware

Written in embedded C/C++ for Arduino IDE using `Adafruit_GFX` and `Adafruit_SSD1306` libraries.

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

int soilPin = A0;
int lightPin = A1;

void setup() {
  Serial.begin(9600);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.display();
}

void setMood(String line1, String line2) {
  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(10, 0);
  display.println("Plant Mood");
  display.drawLine(0, 10, 127, 10, SSD1306_WHITE);
  display.setTextSize(2);
  display.setCursor(10, 20);
  display.println(line1);
  display.setTextSize(1);
  display.setCursor(10, 50);
  display.println(line2);
  display.display();
}

void loop() {
  int soil = analogRead(soilPin);
  int light = analogRead(lightPin);

  if (soil < 300 && light < 300) {
    setMood("Cold &", "Thirsty!");
  } else if (soil < 300 && light >= 300) {
    setMood("Water", "Me Please!");
  } else if (soil >= 300 && light < 300) {
    setMood("Sleepy", "zzz...");
  } else {
    setMood("I Feel", "Great! JAY");
  }

  delay(2000);
}
```

Full source: [`plant_mood_monitor.ino`](./plant_mood_monitor.ino)

---

## ✅ Simulation Results

All four mood states were tested by varying simulated soil/light inputs — all passed with correct OLED output and no false triggers near the threshold boundary.

| Test Case | Soil | Light | Output | Result |
|---|---|---|---|---|
| Wet soil, dark room | ~180 | ~150 | "Cold & Thirsty!" | ✅ Pass |
| Wet soil, bright light | ~200 | ~650 | "Water Me Please!" | ✅ Pass |
| Dry soil, dark room | ~750 | ~140 | "Sleepy zzz..." | ✅ Pass |
| Dry soil, bright light | ~800 | ~700 | "I Feel Great!" | ✅ Pass |

---

## 🌟 Advantages

- Low-cost, easily available components
- Human-readable feedback instead of raw sensor data
- Fast 2-second real-time refresh cycle
- Compact, desktop/windowsill-friendly footprint
- Easily expandable (temperature, humidity, wireless)
- Uses well-documented open-source libraries

## ⚠️ Limitations

- Fixed threshold not calibrated per soil type/plant species
- Resistive soil sensors are corrosion-prone over time
- No historical data logging
- No wireless/remote notifications
- LDR response is non-linear, sensitive to artificial lighting
- Doesn't account for temperature

## 🚀 Future Scope

- Wi-Fi integration (ESP32/ESP8266) for remote monitoring
- DHT11/DHT22 sensor for temperature & humidity
- Adaptive/self-calibrating thresholds
- Automated watering via relay + pump
- Cloud/SD card data logging (Firebase, ThingSpeak)
- Solar-powered off-grid deployment

---

## 🎓 Course Context

Built as part of the **ESSCI-certified Embedded Systems and IoT Engineering course (ELE/Q1404)** at SRM University AP, applying:
- Analog sensor interfacing
- I2C communication
- Conditional logic design
- Embedded C/C++ programming

---

## 🙋 Author

**Galla Indranag**

B.Tech ECE, NRI Institute of Technology, Guntur

Embedded Systems & IoT Engineering (ESSCI-certified), SRM 

- GitHub: [@indra675](https://github.com/indra675)
- LinkedIn: [linkedin.com/in/gallaindranag](https://linkedin.com/in/gallaindranag)

