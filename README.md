# ✈️ Voice-Driven Aerospace Emergency Co-Pilot (Prescriptive QRH Engine)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-brightgreen.svg)](https://www.python.org/)
[![WebGL](https://img.shields.io/badge/Front--End-WebGL%20%2F%20Canvas2D-orange.svg)](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)
[![Gnani.ai Stack](https://img.shields.io/badge/AI%20Stack-Gnani.ai%20v3.0-purple.svg)](https://gnani.ai/)

An interactive, browser-native aerospace safety assistant built for single-pilot and UAV emergency management. The platform couples a self-contained WebGL/Canvas2D 6-DoF flight dynamics engine with an in-cockpit Glass Heads-Up Display (HUD) and **Gnani.ai's Sovereign Voice API Suite** (Prisma v3.0 STT, Evon v3 NLU, Timbre v2.5 TTS).

Unlike traditional glass cockpits that are strictly **descriptive** (displaying warnings like `OIL PRESS LOW` or `ENG 1 FAIL` without instructing the pilot what to do), this system is **prescriptive**. It ingests live flight telemetry, guides the pilot step-by-step through official Quick Reference Handbooks (QRH), listens to spoken decisions via Push-To-Talk (PTT), and actively audits pilot actions against handbook tolerances in real time.

---

## 📑 Table of Contents

* [Key Features](#-key-features)
* [Descriptive vs. Prescriptive Avionics](#-descriptive-vs-prescriptive-avionics)
* [System Architecture](#-system-architecture)
* [AI Stack Integration (Gnani.ai)](#-ai-stack-integration-gnaniai)
* [Repository Structure](#-repository-structure)
* [Quick Start](#-quick-start)
* [Verification & Testing](#-verification--testing)
* [License & Acknowledgments](#-license--acknowledgments)

---

## ✨ Key Features

* 🎯 **Prescriptive Emergency Guidance:** Shifts emergency management from passive alert chimes to active voice guidance, prescribing exact memory items from official flight manuals (**Cessna 172S POH** & **Boeing 737 QRH**).
* 🎙️ **Hands-Free Push-to-Talk (PTT) Cockpit Overlay:** Rendered directly inside the simulation canvas via dynamic HUD overlays and physical hotkey listeners (Spacebar / Yoke Trigger).
* 🔍 **Closed-Loop Action Verification ("Did I Do It Wrong?"):** Automatically parses spoken flight parameters (e.g., target glide speed) and compares them against official handbook rules. If a pilot incorrectly sets 100 KIAS during engine failure, the system immediately issues a warning: *"WARNING! 100 KIAS is TOO HIGH! Target glide speed is 68 KIAS."*
* 🌐 **Multilingual Sovereign Voice Stack:** Automatic spoken language identification (`indic-auto` supporting English, Hindi, Kannada, Tamil, Telugu, Marathi, etc.) with natural voice feedback synthesized in the pilot's native language.
* 🛩️ **Browser-Native 6-DoF Simulation Engine:** Zero external hardware dependencies. Features aerodynamic flight dynamics, windmilling propeller drag calculations, stall limits, and speed-hold pitch assist.
* 📊 **Dual Visual/Auditory Redundancy:** High-contrast Glass Cockpit HUD banner ensures situational awareness even under loud cockpit noise regimes.

---

## ⚡ Descriptive vs. Prescriptive Avionics

| Feature / Dimension | Traditional Glass Cockpit (G1000 / EICAS) | Our Voice AI Emergency Co-Pilot | 
| :--- | :--- | :--- | 
| **System Role** | **Descriptive** (Identifies failure state) | **Prescriptive & Actionable** (Guides recovery steps) | 
| **Cockpit Output** | Flashes warning lights (`OIL PRESS LOW`, `ENG FAIL`) | Speaks exact step-by-step memory items from Cessna/Boeing QRH | 
| **Pilot Workload** | High cognitive load (Pilot must find checklist & recall target speeds) | Zero manual search (Pilot listens and executes hands-free) | 
| **Safety Net** | Passive (Does not detect if pilot executes wrong action) | Active Closed-Loop Audit (Detects unsafe pilot speech/inputs instantly) | 
| **Communication** | Monolingual text/chimes on panel screens | Multilingual Voice Echo (Responds in pilot's native language) | 

---

## 🏗️ System Architecture

```
+---------------------------------------------------------------------------------------+
|                             BROWSER FRONTEND (HTML5 / ES6)                            |
|                                                                                       |
|  +-------------------------------------+   +---------------------------------------+  |
|  | 1. Embedded 3D Flight Viewport      |   | 2. In-Cockpit Glass HUD Overlay       |  |
|  | • Dynamic Horizon & Terrain Renderer|   | • Dynamic Emergency Action Steps      |  |
|  | • Cessna 172 6-DoF Physics Engine  |   | • Native [ 🎙️ PTT BUTTON ] Controls   |  |
|  +-------------------------------------+   +---------------------------------------+  |
|                                     \         /                                       |
|                                Web Audio API / HTTP                                   |
|                                     v         v                                       |
|  +---------------------------------------------------------------------------------+  |
|  |                        PYTHON BACKEND SERVER (app.py)                           |  |
|  |                                                                                 |  |
|  | • Aerodynamic State Monitor (Pitch, Altitude, Windmilling Drag)                |  |
|  | • QRH Rule Machine (Cessna 172S POH Section 3 & Boeing 737 QRH Datasets)        |  |
|  | • Parameter Verification Engine ("Did I Do It Wrong?" Validation Logic)        |  |
|  +---------------------------------------------------------------------------------+  |
|                                         |                                             |
|                                 External API Calls                                    |
|                                         v                                             |
|  +---------------------------------------------------------------------------------+  |
|  |                         GNANI.AI SOVEREIGN AI STACK                             |  |
|  |                                                                                 |  |
|  | • Prisma v3.0 (STT): Audio transcription + multilingual auto-detection (indic-auto)|
|  | • Evon v3 (NLU/Reasoning): Emergency intent & parameter extraction               |  |
|  | • Timbre v2.5 (TTS): Native-language voice feedback generation                     |  |
|  +---------------------------------------------------------------------------------+  |
+---------------------------------------------------------------------------------------+
```

---

## 🤖 AI Stack Integration (Gnani.ai)

The project integrates **Gnani.ai's Sovereign API Suite** for low-latency aerospace communications:

1. **Gnani Prisma v3.0 (STT):** Transcribes pilot headset microphone audio and auto-detects language using the `indic-auto` model.
2. **Gnani Evon v3 (NLU):** Extracts operational intent and spoken key parameters (e.g., target glide airspeed in KIAS, squawk codes, altitude targets).
3. **Gnani Timbre v2.5 (TTS):** Synthesizes natural co-pilot speech feedback using voice models like `Nalini`, matching the exact language spoken by the pilot.

---

## 📁 Repository Structure

```text
emergency-copilot/
├── app.py                   # Flask API Server & Speech Proxy Backend
├── config.py                # Configuration management & environment flags
├── gnani_client.py          # Interface wrapper for Gnani STT, NLU, and TTS APIs
├── qrh.py                   # Quick Reference Handbook engine & state machine
├── detector.py              # Telemetry anomaly & emergency event detector
├── telemetry.py             # Aircraft telemetry listener & state builder
├── test_core.py             # Automated unit tests for QRH parsing and speed rules
├── qrh_database.json        # Official Cessna 172S POH & Boeing 737 QRH rules
├── web/
│   ├── index.html           # WebGL cockpit canvas viewport & Glass HUD overlay
│   └── js/
│       ├── sim.js           # 6-DoF aerodynamic glide engine & speed-hold assist
│       ├── world.js         # WebGL/Canvas2D 3D terrain & horizon renderer
│       ├── cockpit.js       # Dynamic glass cockpit gauges (ASI, Horizon, Alt, VSI, RPM)
│       └── layout.js        # Instrument panel geometry & interactive hit-test regions
├── SETUP_GUIDE.md           # Deployment and testing guide
└── requirements.txt         # Python dependency manifest
```

---

## ⚡ Quick Start

### Prerequisites
* **Python:** 3.10 or higher
* **Node.js:** 18+ (optional, for browser test runner)
* **Gnani.ai API Key:** Obtain from [Gnani.ai Portal](https://gnani.ai/)

### Installation & Execution

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/your-username/emergency-copilot.git
   cd emergency-copilot
   ```

2. **Set Up Virtual Environment & Dependencies:**
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Configure Environment Variables:**
   ```bash
   export GNANI_API_KEY="your_gnani_api_key_here"
   ```

4. **Launch Application:**
   ```bash
   python3 app.py
   ```
   Open your browser and navigate to `http://localhost:5000`.

---

## 🧪 Verification & Testing

Run the test suite to validate parameter extraction, speed boundary verification, and offline speech fallback mechanisms:

```bash
python3 test_core.py
```

### Expected Output Summary:
* **Correct Target Speed (68 KIAS):**
  * `INPUT`: `"setting airspeed to 68 knots"`
  * `RESULT`: `PASS` | *"Good decision, Captain! 68 knots matches QRH target."*
* **Excessive Glide Speed (100 KIAS):**
  * `INPUT`: `"holding 100 knots"`
  * `RESULT`: `FAIL` | *"WARNING! 100 KIAS is TOO HIGH for glide! Target speed is 68 KIAS."*

---

## 📜 License & Acknowledgments

* **License:** Distributed under the MIT License.
* **Aviation Datasets:** Sourced from official **FAA AC 43.13-1B**, **Cessna 172S POH/QRH (Section 3)**, and **Boeing 737 QRH**.
* **Special Thanks:** [Gnani.ai](https://gnani.ai/) sovereign speech services.