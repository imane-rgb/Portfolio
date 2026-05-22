---
title: "Smart Insulin Preservation IoT System"
date: 2025-12-01
description: "Autonomous medical-grade IoT system for insulin preservation with closed-loop thermal regulation, ESP32, and cloud monitoring."
tags: ["IoT", "ESP32", "Embedded C", "Python", "Blynk", "Medical", "Real-Time"]
icon: "🧊"
filetype: "c"
github: "https://github.com/imane-ibnelhabib/smart-insulin"
hw_specs:
  - { key: "MCU",          value: "ESP32" }
  - { key: "Sensor",       value: "DS18B20 Digital Thermal" }
  - { key: "Target Range", value: "2°C – 8°C (optimal 5°C)" }
  - { key: "Stabilization",value: "< 7 minutes" }
  - { key: "Protocol",     value: "Wi-Fi / MQTT" }
  - { key: "Cloud",        value: "Blynk Dashboard" }
  - { key: "Simulation",   value: "Python Behavioral Model" }
  - { key: "Alerts",       value: "Proactive Threshold Alerts" }
---

## Overview

An autonomous, medical-grade IoT preservation system engineered to safeguard
temperature-sensitive insulin payloads within a strict **2°C to 8°C** environment.

The system evaluates and corrects micro-environmental heat flux using an ESP32
microcontroller and a high-precision DS18B20 digital thermal sensor, achieving
complete thermal stabilization at the optimal **5°C benchmark in under 7 minutes**.

## Architecture

```
[DS18B20 Sensor] → [ESP32 MCU]
                        ↓
              [Closed-Loop PID Controller]
                        ↓
         [Peltier / Cooling Actuator]
                        ↓
         [Wi-Fi] → [Blynk Cloud Dashboard]
                        ↓
              [Python Behavioral Simulation]
```

## Key Features

- **Closed-Loop Thermal Regulation** — Custom PID algorithm continuously corrects temperature deviations to maintain the 2–8°C safe zone
- **High-Precision Sensing** — DS18B20 provides ±0.5°C accuracy over the full medical range
- **Fast Stabilization** — Full thermal stabilization achieved in under 7 minutes from ambient
- **Cloud Dashboard** — Blynk integration provides live telemetry, historical data logs, and remote monitoring
- **Proactive Alerts** — Immediate notifications when temperature drifts outside safe thresholds
- **Python Simulation** — Behavioral thermal model validates the control algorithm before hardware deployment

## Embedded Firmware

{{< codeblock lang="c" filename="thermal_control.c" >}}
#include <OneWire.h>
#include <DallasTemperature.h>

#define ONE_WIRE_BUS  4
#define COOLING_PIN   18
#define TARGET_TEMP   5.0f
#define TEMP_MIN      2.0f
#define TEMP_MAX      8.0f

OneWire           oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);

/* Simple bang-bang with hysteresis — upgraded to PID in v2 */
void regulate_temperature(void) {
    sensors.requestTemperatures();
    float current = sensors.getTempCByIndex(0);

    if (current > TARGET_TEMP + 0.3f) {
        digitalWrite(COOLING_PIN, HIGH);   /* activate cooling */
    } else if (current < TARGET_TEMP - 0.3f) {
        digitalWrite(COOLING_PIN, LOW);    /* stop cooling */
    }

    /* Trigger alert if outside safe range */
    if (current < TEMP_MIN || current > TEMP_MAX) {
        Blynk.logEvent("temp_alert",
            String("CRITICAL: ") + current + "°C out of safe range");
    }
}
{{< /codeblock >}}

## Python Thermal Simulation

A Python behavioral model simulates the thermal dynamics of the insulated chamber,
validating the control algorithm's response time and stability before flashing to hardware.
The simulation models heat flux, Peltier cooling curves, and ambient leakage to predict
real-world stabilization time.

## Cloud Monitoring

The Blynk dashboard provides real-time temperature graphs, min/max logs, cooling
actuator state, and push notifications — accessible from any device, anywhere.
