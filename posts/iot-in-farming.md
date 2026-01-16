---
title: "IoT in Modern Farming: Smart Agriculture Project"
slug: iot-in-farming
description: "A smart agriculture monitoring system for a high school science festival using ESP32, Blynk, and SIM900A."
tags:
  - iot
  - esp32
  - arduino
  - projects
added: "Jan 01 2024"
---

## Project Overview

For a high school science festival, I developed a smart agriculture monitoring system using an ESP32-CAM, Blynk IoT platform, and a SIM900A GSM module. The project demonstrates how IoT can automate and monitor key environmental factors in farming, making agriculture smarter and more efficient.

## Hardware Architecture

The system is built around the **ESP32-CAM**, chosen for its Wi-Fi capabilities and ample GPIOs. It interfaces with a suite of sensors to gather environmental data:

- **ESP32-CAM:** The brain of the operation.
- **SIM900A GSM Module:** Handles SMS alerts and incoming calls (Connected via UART1).
- **Sensors:**
  - **DHT11:** Digital temperature & humidity.
  - **Capacitive Soil Moisture:** Analog input (mapped 0-100%).
  - **Rain Sensor, pH Sensor, LDR:** For comprehensive environmental data.
- **Actuator:** 5V Relay controlling a water pump (Active Low trigger on Pin 4).
- **Display:** 20x4 I2C LCD for real-time local monitoring.

## The Automation Logic

The core loop runs every 2 seconds, reading all sensor data and converting analog values (0-4095) to human-readable percentages. The most critical feature is the **automatic irrigation system**:

```cpp
// Read sensor values
int moistureLevel = analogRead(MOISTURE_PIN);
// Convert 12-bit ADC value to percentage (Inverted: 4095 is dry, 0 is wet)
int moisturePercentage = map(moistureLevel, 0, 4095, 100, 0);

// Control the relay based on moisture level
if (moisturePercentage <= 40) {
    digitalWrite(RELAY_PIN, LOW);  // Turn ON the water pump (Active Low)
    Serial.println("Relay Status: ON ");
} else {
    digitalWrite(RELAY_PIN, HIGH); // Turn OFF the water pump
    Serial.println("Relay Status: OFF ");
}
```

## GSM Connectivity & Remote Access

Internet connectivity isn't always reliable on farms. To solve this, I integrated a **SIM900A GSM module**. The system can listen for incoming calls and respond with an SMS containing the latest sensor readings.

```cpp
// Function to send SMS via SIM900A using AT Commands
void sendSMS(const char* number, const char* message) {
    String cmd = "AT+CMGS=\"" + String(number) + "\"\r\n";
    sendATCommand(cmd.c_str(), 1000); // Initiate SMS mode
    delay(500);
    sim900.print(message);            // Send the message body
    delay(500);
    sim900.write(26);                 // Send Ctrl+Z to finalize
    Serial.println("SMS sent!");
}
```

## Blynk Integration

For real-time visual monitoring, the system pushes data to the **Blynk IoT Cloud** using Virtual Pins. This allows farmers to check the status of their field from anywhere in the world using the mobile app.

- **V0:** Temperature
- **V1:** Humidity
- **V2:** Soil Moisture
- **V3:** Rain Status

## Conclusion

This project was a nice challenge in integrating mixed-signal sensors, GSM communication, and cloud IoT platforms. It successfully demonstrated how low-cost hardware can solve real-world agricultural problems. The full source code is available below.

[**View Full Source Code on GitHub Gist →**](https://gist.github.com/abhijithabhiakl/06ba39dfe6ed61c86853791f832e5c92)
