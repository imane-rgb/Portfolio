---
title: "Smart Tourism & Reservation Platform"
date: 2025-10-01
description: "IoT-driven tourism platform with real-time environmental telemetry, intelligent booking, and live web dashboard."
tags: ["IoT", "Python", "Web", "Real-Time", "Embedded", "Full-Stack"]
icon: "🗺️"
filetype: "py"
github: "https://github.com/imane-ibnelhabib/smart-tourism"
hw_specs:
  - { key: "Sensors",      value: "Crowd, AQI, Micro-climate" }
  - { key: "Protocol",     value: "MQTT / WebSocket" }
  - { key: "Backend",      value: "Python / FastAPI" }
  - { key: "Database",     value: "InfluxDB / PostgreSQL" }
  - { key: "Dashboard",    value: "Real-Time Web UI" }
  - { key: "Features",     value: "Smart Parking, Booking Engine" }
  - { key: "Alerts",       value: "Proactive Incident Alerts" }
  - { key: "Deployment",   value: "Docker / Cloud" }
---

## Overview

An intelligent, IoT-driven tourism and automated reservation platform designed to modernize
the travel ecosystem through real-time environmental telemetry.

The system evaluates live on-site metrics — crowd density, localized micro-climates, and
air quality index (AQI) — to dynamically guide visitor traffic and prevent site congestion.

## Architecture

```
[Sensor Nodes] → MQTT → [Backend / FastAPI]
                               ↓
                    [InfluxDB + PostgreSQL]
                               ↓
              [Web Dashboard] ← WebSocket → [Booking Engine]
                               ↓
                    [Smart Parking Nodes]
```

## Key Features

- **Real-Time Telemetry** — Live crowd density, AQI, and micro-climate monitoring from distributed sensor nodes
- **Intelligent Booking Engine** — Capacity-aware reservation system with live threshold enforcement to prevent congestion
- **Smart Parking Management** — Connected parking nodes with real-time availability tracking
- **Centralized Dashboard** — Interactive web UI with live metrics, security logs, and incident alerts
- **Proactive Alerts** — Automated notifications when thresholds are exceeded

## Embedded Layer

Sensor simulation nodes publish telemetry payloads over MQTT every few seconds. Each node
reports crowd count, temperature, humidity, and AQI readings, which the backend ingests,
validates, and stores in InfluxDB for time-series analysis.

{{< codeblock lang="python" filename="telemetry_publisher.py" >}}
import paho.mqtt.client as mqtt
import json, time, random

BROKER = "localhost"
TOPIC  = "tourism/site/node_01"

client = mqtt.Client()
client.connect(BROKER, 1883)

while True:
    payload = {
        "crowd_density": random.randint(20, 95),   # percent
        "temperature":   round(random.uniform(18, 38), 1),
        "humidity":      round(random.uniform(30, 80), 1),
        "aqi":           random.randint(10, 150),
        "timestamp":     time.time()
    }
    client.publish(TOPIC, json.dumps(payload))
    time.sleep(5)
{{< /codeblock >}}

## Booking Engine

The reservation system queries live capacity data before confirming bookings. If a site
is above its configured threshold, the engine redirects visitors to alternative time slots
or nearby locations, distributing traffic intelligently.

## Dashboard

A full-stack web dashboard provides real-time visualization of all sensor streams,
an interactive map of site nodes, security event logs, and a management interface
for operators to configure thresholds and respond to incidents.
