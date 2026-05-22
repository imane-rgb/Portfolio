---
title: "AI-Powered Access Control System"
date: 2025-11-01
description: "Smart IoT access control with facial recognition using YOLOv8, ESP32-CAM, and a real-time web dashboard."
tags: ["AI", "Computer Vision", "IoT", "ESP32", "Python", "Flask", "YOLOv8"]
icon: "🔐"
filetype: "py"
github: "https://github.com/imane-ibnelhabib/ai-access-control"
hw_specs:
  - { key: "Hardware",     value: "ESP32-CAM" }
  - { key: "Detection",    value: "YOLOv8" }
  - { key: "Embeddings",   value: "Deep Learning (FaceNet)" }
  - { key: "Backend",      value: "Python / Flask" }
  - { key: "Protocol",     value: "HTTP / WebSocket" }
  - { key: "Access HW",    value: "Relay / Door Lock" }
  - { key: "Dashboard",    value: "Real-Time Web UI" }
  - { key: "Features",     value: "User Mgmt, Security Logs" }
---

## Overview

A smart IoT access control system powered by artificial intelligence and facial recognition.
The system detects and recognizes faces in real time using YOLOv8 and deep learning embeddings,
automating physical access control through connected hardware.

## Architecture

```
[ESP32-CAM] → HTTP stream → [Flask Server]
                                  ↓
                    [YOLOv8 Detection + FaceNet]
                                  ↓
                    [Identity Match / Reject]
                                  ↓
              [Relay → Door Lock]   [Web Dashboard]
```

## Key Features

- **Real-Time Face Detection** — YOLOv8 model running on the Flask server processes the ESP32-CAM stream frame by frame
- **Identity Recognition** — Deep learning embeddings match detected faces against a registered user database
- **Hardware Integration** — Relay-controlled door lock triggered automatically on successful recognition
- **Web Dashboard** — Live camera feed, access logs, user enrollment, and security event management
- **Security Logs** — Every access attempt (granted or denied) is timestamped and stored

## Detection Pipeline

{{< codeblock lang="python" filename="face_recognition.py" >}}
from ultralytics import YOLO
import face_recognition
import numpy as np
import cv2

model = YOLO("yolov8n-face.pt")

def process_frame(frame, known_encodings, known_names):
    results = model(frame, verbose=False)[0]
    for box in results.boxes:
        x1, y1, x2, y2 = map(int, box.xyxy[0])
        face_crop = frame[y1:y2, x1:x2]
        rgb_crop  = cv2.cvtColor(face_crop, cv2.COLOR_BGR2RGB)

        encodings = face_recognition.face_encodings(rgb_crop)
        if not encodings:
            continue

        matches   = face_recognition.compare_faces(known_encodings, encodings[0])
        distances = face_recognition.face_distance(known_encodings, encodings[0])
        best_idx  = np.argmin(distances)

        name = known_names[best_idx] if matches[best_idx] else "Unknown"
        cv2.rectangle(frame, (x1, y1), (x2, y2),
                      (0, 255, 0) if name != "Unknown" else (0, 0, 255), 2)
        cv2.putText(frame, name, (x1, y1 - 8),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255, 255, 255), 2)
    return frame
{{< /codeblock >}}

## ESP32-CAM Integration

The ESP32-CAM streams MJPEG frames over HTTP to the Flask server. The server processes
each frame through the detection pipeline and sends access grant/deny signals back to
the ESP32, which controls the relay connected to the door lock mechanism.

## Dashboard

A modern web interface provides live camera monitoring, a searchable access log,
user enrollment (add/remove faces), and real-time alerts for unrecognized access attempts.
