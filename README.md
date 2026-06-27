# 🦟 GUARDIAN — Local AI Mosquito Detection & Ultrasonic Repellent System

<div align="center">

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![Ollama](https://img.shields.io/badge/Ollama-000000?style=for-the-badge&logo=ollama&logoColor=white)
![Web Audio API](https://img.shields.io/badge/Web%20Audio%20API-FF6B35?style=for-the-badge&logo=webaudio&logoColor=white)
![WebRTC](https://img.shields.io/badge/WebRTC-333333?style=for-the-badge&logo=webrtc&logoColor=white)

**Single-File Local AI Mosquito Detection with Ultrasonic Repellent Engine**  
_Live Webcam → Ollama Vision (llava / bakllava / moondream) → Bounding Box Overlays → Web Audio Repellent_

![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=flat-square)
![Architecture](https://img.shields.io/badge/Architecture-Single%20HTML%20File-blue?style=flat-square)
![Inference](https://img.shields.io/badge/Inference-100%25%20Local-orange?style=flat-square)

</div>

---

## 📋 Overview

**GUARDIAN** is a single-file HTML application that uses a live webcam feed and local Ollama vision models to detect mosquitoes in real time, draws bounding box overlays on detected insects, and activates a Web Audio API ultrasonic repellent tone engine — all running in the browser with zero backend, zero cloud, and zero dependencies.

The entire application — webcam capture, canvas rendering, Ollama API integration, bounding box overlay, Web Audio synthesis, waveform visualization, and holographic HUD — ships in a single `.html` file.

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                 GUARDIAN — Single-File Browser Architecture                  │
└─────────────────────────────────────────────────────────────────────────────┘

  ┌──────────────────────────────────────────────────────────────┐
  │  CAMERA INPUT LAYER                                           │
  │                                                              │
  │  getUserMedia (three-level fallback):                        │
  │  Level 1: Exact {width:1280, height:720} constraints        │
  │  Level 2: Ideal constraints (width/height as hints)         │
  │  Level 3: {video: true} — bare minimum fallback             │
  │                                                              │
  │  Mobile: facingMode:'environment' (rear camera preferred)   │
  │  iOS: autoplay + playsinline + muted attributes enforced    │
  └──────────────────────────┬───────────────────────────────────┘
                             │ Video stream
                             ▼
  ┌──────────────────────────────────────────────────────────────┐
  │  CANVAS RENDER LAYER                                          │
  │  ─ Video frames drawn to <canvas> at configurable FPS       │
  │  ─ Frame captured as base64 JPEG for vision inference       │
  │  ─ Bounding boxes overlaid on detection results             │
  │  ─ HUD overlay drawn (scan lines, corner brackets, status)  │
  └──────────────────────────┬───────────────────────────────────┘
                             │ base64 JPEG frame
                             ▼
  ┌──────────────────────────────────────────────────────────────┐
  │  OLLAMA VISION INFERENCE LAYER                                │
  │                                                              │
  │  Supported models (user-selectable):                        │
  │  ─ llava        — LLaVA vision model                        │
  │  ─ bakllava     — BakLLaVA variant                          │
  │  ─ moondream    — Lightweight edge vision model             │
  │                                                              │
  │  Prompt: structured JSON-requesting detection prompt        │
  │  Response: JSON with detected objects + bounding box coords │
  │  Endpoint: http://localhost:11434/api/generate              │
  └──────────────────────────┬───────────────────────────────────┘
                             │ Detection results (JSON)
                             ▼
  ┌──────────────────────────────────────────────────────────────┐
  │  BOUNDING BOX OVERLAY ENGINE                                  │
  │  ─ Parses detection JSON: [{label, x, y, width, height}]    │
  │  ─ Draws labelled bounding rectangles on canvas              │
  │  ─ Color-coded by detection confidence                      │
  │  ─ Click-drag selection: Object Info Mode                   │
  │    (draw selection rect → AI describes selected region)      │
  └──────────────────────────┬───────────────────────────────────┘
                             │ Detection event
                             ▼
  ┌──────────────────────────────────────────────────────────────┐
  │  WEB AUDIO REPELLENT ENGINE                                   │
  │                                                              │
  │  AudioContext + OscillatorNode:                             │
  │  ─ Ultrasonic frequency synthesis                           │
  │  ─ Frequency sweep patterns (configurable range)            │
  │  ─ GainNode for volume envelope                             │
  │  ─ AnalyserNode → real-time waveform visualization          │
  │  ─ CanvasRenderingContext2D waveform plot                   │
  └──────────────────────────────────────────────────────────────┘

  ┌──────────────────────────────────────────────────────────────┐
  │  HOLOGRAPHIC HUD (CSS + Canvas)                               │
  │  ─ Dark cyber aesthetic — deep black background              │
  │  ─ Cyan/teal accent lines and corner brackets                │
  │  ─ CRT scan line overlay (CSS repeating-linear-gradient)    │
  │  ─ Detection counter, model selector, status indicator      │
  │  ─ Safe-area CSS insets for iOS notch/home-indicator        │
  └──────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Technology Stack

| Component | Technology | Purpose |
|---|---|---|
| **Architecture** | Single `.html` file | Self-contained — no build step, no backend, no npm |
| **Camera Capture** | `navigator.mediaDevices.getUserMedia` (WebRTC) | Live webcam stream with three-level fallback |
| **Frame Rendering** | HTML5 `<canvas>` + `CanvasRenderingContext2D` | Video frame drawing and overlay rendering |
| **Vision Models** | Ollama (`llava`, `bakllava`, `moondream`) | Local AI object detection from frame images |
| **Vision API** | `fetch()` → `localhost:11434/api/generate` | Ollama REST API for image inference |
| **Audio Synthesis** | Web Audio API (`AudioContext`, `OscillatorNode`) | Ultrasonic repellent tone generation |
| **Waveform** | `AnalyserNode` + `CanvasRenderingContext2D` | Real-time audio waveform visualization |
| **UI / HUD** | CSS + Canvas | Holographic dark cyber aesthetic |
| **Mobile** | `facingMode`, `playsinline`, CSS safe-area | iOS and Android browser compatibility |

---

## ✨ Key Features

### 📷 Multi-Level Camera Initialization
```
getUserMedia attempt 1: exact {width:1280, height:720}
    ↓ fail
getUserMedia attempt 2: ideal width/height constraints
    ↓ fail
getUserMedia attempt 3: {video: true} bare fallback
    ↓ success
Stream bound to <video> element
```
- Covers edge cases across device types, browser versions, and camera permission states
- iOS-specific: `autoplay`, `playsinline`, `muted` attributes required for camera stream to play

### 🤖 Ollama Vision Detection (Local)
- Video frame captured from canvas as JPEG base64 at configurable intervals
- Sent to local Ollama endpoint with a structured detection prompt requesting JSON output
- Response parsed: detected objects, bounding box coordinates, labels
- Zero data leaves the local network — 100% private inference

### 📦 Bounding Box Overlay
- Detected object coordinates rendered as labelled bounding rectangles directly on the video canvas
- Color-coded by object type / confidence
- **Object Info Mode** — click and drag to draw a selection rectangle; the selected canvas region is sent to Ollama vision asking for a detailed description of the enclosed area

### 🔊 Web Audio Ultrasonic Repellent Engine
- `AudioContext` with `OscillatorNode` generates tone in the ultrasonic frequency range
- Frequency sweep patterns (ascending, descending, pulsed) cycling at configurable rate
- `GainNode` controls volume envelope for ramp-on / ramp-off to prevent audio click artifacts
- `AnalyserNode` feeds real-time frequency data to a waveform canvas plot
- Toggle: detection triggers automatic repellent activation

### 📡 Waveform Visualization
- `AnalyserNode.getByteTimeDomainData()` sampled at animation frame rate
- Waveform drawn on a dedicated canvas panel in the HUD
- Oscilloscope-style rendering with cyan trace on dark background

### 🖥️ Holographic HUD Aesthetic
- Full dark background (`#000814`)
- Cyan/teal (`#00fff7`) accent lines, corner bracket elements, and status indicators
- CRT scan line CSS overlay (`repeating-linear-gradient`) for retro-holographic feel
- CSS `env(safe-area-inset-*)` for iOS notch and home indicator clearance

---

## 🔄 Application Workflow

```
PAGE LOAD
  └─ Camera permission requested → three-level fallback initialization
  └─ VideoStream bound to <video> element
  └─ Canvas initialized for frame rendering + overlay

DETECTION LOOP (configurable interval, default: 2s)
  └─ drawImage(video) → canvas frame capture
  └─ canvas.toDataURL('image/jpeg', 0.7) → base64 string
  └─ POST to localhost:11434/api/generate with base64 + detection prompt
  └─ Parse JSON response → extract detected object list

BOUNDING BOX DRAW
  └─ For each detected object: draw rect + label on canvas overlay
  └─ Detection count incremented in HUD status bar

OBJECT INFO MODE (on click-drag)
  └─ User draws selection rectangle
  └─ Selected canvas region cropped → base64 encoded
  └─ Sent to Ollama vision: "describe what is in this region"
  └─ Response displayed in HUD info panel

REPELLENT ENGINE (on mosquito detection / manual toggle)
  └─ AudioContext.resume() (required by browser autoplay policy)
  └─ OscillatorNode.frequency set → sweep pattern started
  └─ AnalyserNode feeds waveform canvas at requestAnimationFrame rate

MOBILE
  └─ facingMode: 'environment' (rear camera default on phones)
  └─ iOS autoplay: video attributes set programmatically after getUserMedia resolves
  └─ Safe-area insets applied to HUD containers
```

---

## 📁 Project Structure

```
guardian/
└── index.html     # Complete application — all JS, CSS, and HTML in one file
```

> The entire application ships as a single self-contained HTML file. No build tools, no package manager, no backend required.

---

## 🚀 Getting Started

### Prerequisites

- Ollama running locally with one of the supported vision models:
  ```bash
  ollama pull llava
  # or
  ollama pull moondream
  ```
- Any modern browser (Chrome, Edge, Firefox, Safari iOS 15+)
- Webcam or device camera

### Run

```bash
# No installation needed — open directly:
open guardian/index.html

# Or serve locally for camera permission on non-localhost:
python -m http.server 8080
# → http://localhost:8080/guardian/index.html
```

> **Note:** Browsers require either `localhost` or HTTPS to allow `getUserMedia`. For network access from phone, use a local HTTPS server or browser dev flags.

### Mobile

Navigate to `http://<your-machine-ip>:8080/guardian/index.html` from your phone on the same LAN. Rear camera is automatically selected. iOS users: ensure browser permissions are granted in iOS Settings → Safari → Camera.

---

## 👤 Author

**Shrikant Khating**  
Senior BI Developer | AI/ML Practitioner | Browser Systems Engineer  
📧 shri.khating@gmail.com  
🔗 [LinkedIn](https://linkedin.com/in/shrikant-khating) · [GitHub](https://github.com/shrikhating)

> *Built as a solo personal innovation lab project — no team, no budget, production-grade engineering.*
