<p align="center">
  <img src="extension/icons/raptor128.png" alt="Raptor AI" width="120" />
</p>

<h1 align="center">Raptor AI</h1>

<p align="center">
  <strong>Autonomous Local-First AI Operating Layer for macOS</strong>
</p>

<p align="center">
  <a href="#features">Features</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#setup">Setup</a> •
  <a href="#usage">Usage</a> •
  <a href="#project-structure">Structure</a> •
  <a href="#license">License</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/python-3.11+-blue?style=flat-square&logo=python" alt="Python" />
  <img src="https://img.shields.io/badge/platform-macOS-lightgrey?style=flat-square&logo=apple" alt="macOS" />
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="License" />
  <img src="https://img.shields.io/badge/AI-Local--First-orange?style=flat-square" alt="Local AI" />
</p>

---

## Overview

**Raptor AI** is an autonomous, voice-activated AI assistant that operates as a personal **AI operating layer** — not just a chatbot. It runs entirely on your local machine, proactively monitors system health, network activity, and external intelligence sources, and adapts its behavior based on your preferences over time.

Unlike conventional assistants that wait passively for commands, Raptor **thinks ahead**: it detects anomalies, alerts intelligently, automates OS-level tasks, and explains every decision it makes.

---

## Features

| Category | Capabilities |
| :--- | :--- |
| **🎙️ Voice Interface** | Wake-word activation ("Hey Raptor"), local STT via Faster-Whisper, natural TTS |
| **🖥️ System Monitoring** | Real-time CPU, RAM, disk, and battery tracking with anomaly detection |
| **🌐 Network Intelligence** | Local network device scanning, new device alerts, IP change detection |
| **📡 External Intelligence** | Weather forecasts, breaking news, live sports scores (cricket, football) |
| **💬 OS Automation** | Send WhatsApp messages, read Apple Mail, manage files via osascript |
| **🌍 Browser Intelligence** | Google search, page summarization via Chrome extension bridge |
| **🧠 Adaptive Learning** | Priority engine adjusts alert frequency based on your engagement |
| **🔍 Explainability** | Ask "Why did you alert me?" — Raptor explains with confidence scores and history |
| **📊 Real-Time Dashboard** | Next.js frontend with live WebSocket state visualization |

---

## Architecture

Raptor AI employs a **six-layer architecture** with clear separation of concerns:

```
┌──────────────────────────────────────────────────────────┐
│                    PERCEPTION LAYER                      │
│   Wake Listener (OpenWakeWord)  ←→  STT (Faster-Whisper) │
├──────────────────────────────────────────────────────────┤
│                   ORCHESTRATION CORE                     │
│   Agent FSM  →  Intent Planner  →  Intelligence Layer    │
├──────────────────────────────────────────────────────────┤
│               EXECUTION & AUTOMATION LAYER               │
│   Executor  →  Tool Registry  →  OS / Browser / APIs     │
├──────────────────────────────────────────────────────────┤
│                   BACKGROUND DAEMONS                     │
│   Proactive Monitor (System · Network · External)        │
├──────────────────────────────────────────────────────────┤
│                   LEARNING & MEMORY                      │
│   Priority Engine  ←→  Learning Engine  ←→  User Profile │
├──────────────────────────────────────────────────────────┤
│                   PRESENTATION LAYER                     │
│   Local TTS  ←→  WebSocket Bridge  ←→  Next.js Dashboard │
└──────────────────────────────────────────────────────────┘
```

**Dual-Mode Operation:**
- **Reactive Mode:** User speaks → Plan → Execute → Respond (< 2s latency)
- **Proactive Mode:** Monitor → Detect → Evaluate Priority → Alert/Suppress

---

## Setup

### Prerequisites

- **macOS** 12+ (Monterey or later)
- **Python** 3.11+
- **Microphone** access (for voice input)
- **Groq API Key** (free tier available at [console.groq.com](https://console.groq.com))

### Installation

```bash
# Clone the repository
git clone https://github.com/soumyadebtripathy/raptor-ai.git
cd raptor-ai

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.example .env
# Edit .env and add your GROQ_API_KEY
```

### Running

```bash
# Start the agent (recommended — includes watchdog)
python raptor_launcher.py

# Or run the agent directly
python agent.py
```

---

## Usage

### Voice Commands

| Say This | Raptor Does |
| :--- | :--- |
| *"Hey Raptor, what's my system status?"* | Reports CPU, RAM, disk, battery |
| *"Send a WhatsApp to John saying I'll be late"* | Opens WhatsApp, finds contact, sends message |
| *"What's the weather today?"* | Fetches local weather forecast |
| *"Search for latest AI news"* | Opens browser, searches, summarizes results |
| *"Read my emails"* | Reads and summarizes unread Apple Mail |
| *"Play some music"* | Opens YouTube and plays music |

### Learning Controls

| Say This | Effect |
| :--- | :--- |
| *"Always notify me about cricket"* | Sets cricket alerts to ALWAYS priority |
| *"Never alert me about weather"* | Suppresses weather notifications |
| *"Why did you alert me about CPU?"* | Explains confidence, history, and rule used |
| *"Reset my preferences"* | Clears all learned behavior |

---

## Project Structure

```
raptor-ai/
├── agent.py                    # Main agent FSM — central orchestrator
├── raptor_launcher.py          # Watchdog launcher with singleton locking
├── server.py                   # MCP server entry point
├── requirements.txt            # Python dependencies
├── .env.example                # Environment variable template
├── LICENSE                     # MIT License
│
├── core/                       # Core engine modules
│   ├── planner.py              # Intent routing (regex + LLM fallback)
│   ├── executor.py             # Tool execution with safety checks
│   ├── intelligence.py         # Context analysis & threshold evaluation
│   ├── monitor.py              # Proactive background monitoring daemon
│   ├── priority_engine.py      # Dynamic event priority scoring
│   ├── learning_engine.py      # Adaptive behavior from user feedback
│   ├── learning_controls.py    # Explainability & user overrides
│   ├── local_audio.py          # Audio capture & Faster-Whisper STT
│   ├── wake_listener.py        # Wake word detection (OpenWakeWord)
│   ├── browser_bridge.py       # WebSocket bridge to Chrome extension
│   ├── ws_bridge.py            # Frontend dashboard WebSocket bridge
│   ├── health_check.py         # System health utilities
│   ├── config.py               # Global configuration
│   ├── validate_raptor.py      # Startup validation checks
│   │
│   ├── tools/                  # Tool registry modules
│   │   ├── automation.py       # macOS UI automation (WhatsApp, etc.)
│   │   ├── browser.py          # Browser search & summarization
│   │   ├── email.py            # Apple Mail integration
│   │   ├── realtime.py         # Real-time data tools
│   │   ├── system.py           # System info tools
│   │   ├── os.py               # OS-level file operations
│   │   ├── web.py              # Web scraping & API tools
│   │   └── time_tools.py       # Date/time utilities
│   │
│   ├── external_monitors/      # External intelligence sources
│   │   ├── news_monitor.py     # Breaking news polling
│   │   ├── sports_monitor.py   # Live sports score tracking
│   │   └── weather_monitor.py  # Weather forecast monitoring
│   │
│   ├── prompts/                # LLM prompt templates
│   └── resources/              # Static data resources
│
├── extension/                  # Chrome extension for browser bridge
│   ├── manifest.json
│   ├── background.js
│   ├── content.js
│   └── icons/
│
├── dashboard/                  # Next.js real-time dashboard (separate setup)
├── scripts/                    # Launch & utility scripts
├── tests/                      # Test suite
├── docs/                       # Documentation
├── logs/                       # Runtime logs (gitignored)
└── config/                     # Additional configuration files
```

---

## Security & Permissions

Raptor AI requires the following macOS permissions:

| Permission | Purpose |
| :--- | :--- |
| **Microphone** | Voice input capture |
| **Accessibility** | UI automation for WhatsApp, Mail |
| **Full Disk Access** | File management operations |
| **Network** | Local network scanning, API calls |

### Safety Mechanisms

- **Destructive Action Confirmation:** Tools flagged as `destructive` require explicit voice confirmation before execution
- **Protected Events:** Critical alerts (low battery, new network device) cannot be fully suppressed by the learning engine
- **Restricted Domains:** Browser automation is blocked on sensitive sites (banking, login pages)

---

## Technology Stack

| Component | Technology | Rationale |
| :--- | :--- | :--- |
| Wake Word | OpenWakeWord (ONNX) | Local, CPU-efficient, no TF dependency |
| Speech-to-Text | Faster-Whisper (int8) | Real-time local transcription, 100% private |
| Text-to-Speech | pyttsx3 | OS-native, process-isolated |
| LLM Reasoning | Groq (Llama-3.1-8b) | Near-instant inference via LPU |
| System Interface | osascript + psutil | Deep macOS integration |
| Dashboard | Next.js + WebSockets | Real-time state visualization |
| Backend | Python + FastAPI + aiohttp | Async event-driven architecture |

---

## Future Scope

- 🌐 **Browser Intelligence v2** — Full DOM manipulation, form filling, autonomous navigation
- 🔗 **Chained Workflows** — Multi-step task execution across tools
- 👁️ **Visual Perception** — Local vision models for screen awareness
- 🤖 **Multi-Agent** — Coordinated Raptor instances across devices
- 📱 **Cross-Platform** — Linux and mobile support

---

## Author

**Soumyadeb Tripathy**
INT428 — Project-Based Assessment

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
