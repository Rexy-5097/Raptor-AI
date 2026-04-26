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
  <a href="#workflows">Workflows</a> •
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
  <img src="https://img.shields.io/badge/voice-activated-purple?style=flat-square&logo=audiomack" alt="Voice Activated" />
  <img src="https://img.shields.io/badge/learning-adaptive-red?style=flat-square&logo=tensorflow" alt="Adaptive Learning" />
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

### High-Level System Architecture

```mermaid
graph TD
    subgraph Perception["🎙️ Perception Layer"]
        WL["Wake Listener<br/><i>OpenWakeWord (ONNX)</i>"]
        STT["Speech-to-Text<br/><i>Faster-Whisper int8</i>"]
    end

    subgraph Orchestration["🧠 Orchestration Core"]
        Agent["Agent FSM<br/><i>IDLE → LISTEN → PROCESS → SPEAK</i>"]
        Planner["Intent Planner<br/><i>Regex + LLM Fallback</i>"]
        Intel["Intelligence Layer<br/><i>Context & Thresholds</i>"]
    end

    subgraph Execution["⚙️ Execution & Automation"]
        Exec["Action Executor"]
        TR["Tool Registry"]
        OS["OS Automation<br/><i>osascript / psutil</i>"]
        Browser["Browser Bridge<br/><i>WebSocket ↔ Chrome</i>"]
    end

    subgraph Daemons["🔄 Background Daemons"]
        Monitor["Proactive Monitor<br/><i>System · Network · External</i>"]
    end

    subgraph Learning["📚 Learning & Memory"]
        PE["Priority Engine"]
        LE["Learning Engine"]
        UP[("User Profile<br/><i>JSON</i>")]
        Log[("Interaction Log<br/><i>JSON</i>")]
    end

    subgraph Presentation["📊 Presentation Layer"]
        TTS["Local TTS<br/><i>pyttsx3</i>"]
        WS["WebSocket Bridge"]
        Dash["Next.js Dashboard"]
    end

    %% Perception → Orchestration
    WL -- "Wake Word Detected" --> Agent
    STT -- "Transcription" --> Agent

    %% Orchestration → Execution
    Agent -- "Raw Intent" --> Planner
    Planner -- "Parsed Task" --> Exec
    Exec -- "Resolve" --> TR
    TR -- "Execute" --> OS
    TR -- "Execute" --> Browser

    %% Execution → Intelligence
    Exec -- "Result" --> Intel
    Intel -- "Synthesized Response" --> Agent

    %% Daemons → Intelligence
    Monitor -- "Anomalies & Events" --> Intel
    Intel -- "Check Priority" --> PE
    PE -- "Fetch Profile" --> UP

    %% Presentation
    Agent -- "State Updates" --> WS
    WS -- "Real-time Data" --> Dash
    Agent -- "Speak" --> TTS

    %% Learning Loop
    Agent -- "User Feedback" --> LE
    LE -- "Adjust Priorities" --> UP
    LE -- "Record Event" --> Log
```

### Component Interaction Map

```mermaid
graph LR
    subgraph Input["Input Sources"]
        Voice["🎤 Voice"]
        Sensors["📡 System Sensors"]
        APIs["🌐 External APIs"]
    end

    subgraph Processing["Processing Pipeline"]
        ASR["Automatic Speech<br/>Recognition"]
        NLU["Natural Language<br/>Understanding"]
        TaskRouter["Task Router<br/><i>Regex + LLM</i>"]
    end

    subgraph Actions["Action Domains"]
        SysTool["System Tools"]
        AutoTool["Automation Tools"]
        BrowseTool["Browser Tools"]
        ExtTool["External Data Tools"]
    end

    subgraph Output["Output Channels"]
        Speech["🔊 Voice Response"]
        Dashboard["📊 Dashboard"]
        OSAction["💻 OS Actions"]
    end

    Voice --> ASR --> NLU --> TaskRouter
    Sensors --> TaskRouter
    APIs --> TaskRouter

    TaskRouter --> SysTool --> OSAction
    TaskRouter --> AutoTool --> OSAction
    TaskRouter --> BrowseTool --> OSAction
    TaskRouter --> ExtTool --> Speech

    SysTool --> Dashboard
    AutoTool --> Speech
    BrowseTool --> Speech
    ExtTool --> Dashboard
```

### Agent State Machine

```mermaid
stateDiagram-v2
    [*] --> IDLE

    IDLE --> LISTENING : Wake Word Detected
    LISTENING --> PROCESSING : Speech Captured
    PROCESSING --> SPEAKING : Response Ready
    SPEAKING --> IDLE : Utterance Complete

    IDLE --> PROCESSING : Proactive Alert Triggered
    PROCESSING --> IDLE : Alert Suppressed by Priority Engine

    SPEAKING --> LISTENING : Follow-up Detected

    note right of IDLE
        Agent is dormant.
        Wake Listener active.
        Monitor daemons running.
    end note

    note right of PROCESSING
        Planner routes intent.
        Executor runs tools.
        Intelligence analyzes results.
    end note
```

---

## Workflows

### Voice Command Execution

```mermaid
sequenceDiagram
    participant User
    participant Wake as 🎙️ Wake Listener
    participant STT as 🗣️ Local STT
    participant Core as 🧠 Agent Core
    participant Plan as 📋 Planner
    participant Exec as ⚙️ Executor
    participant Tool as 🔧 Tool Registry
    participant TTS as 🔊 TTS Engine

    User->>Wake: "Hey Raptor"
    Wake-->>Core: Wake Word Detected
    Core->>TTS: Play Chime
    Core->>STT: Start Audio Capture
    User->>STT: "What is my CPU usage?"
    STT-->>Core: Transcription Complete
    Core->>Plan: Parse Intent
    Plan-->>Core: {tool: "get_system_info"}
    Core->>Exec: Execute Plan
    Exec->>Tool: Run get_system_info()
    Tool-->>Exec: {CPU: "45%", RAM: "60%"}
    Exec-->>Core: Result Ready
    Core->>TTS: "Your CPU is at 45 percent."
    Core->>Core: Return to IDLE
```

### Proactive Alert & Adaptive Learning

```mermaid
sequenceDiagram
    participant Mon as 🔍 Monitor Daemon
    participant PE as ⚖️ Priority Engine
    participant Agent as 🧠 Agent Core
    participant User as 👤 User
    participant LE as 📚 Learning Engine
    participant UP as 💾 User Profile

    Mon->>Mon: Detect CPU Spike (95%)
    Mon->>PE: Evaluate Event (cpu_spike)
    PE->>UP: Fetch Priority Config
    UP-->>PE: cpu_spike → HIGH
    PE-->>Mon: Priority: HIGH → Allow Alert
    Mon->>Agent: Inject Alert
    Agent->>User: "⚠️ CPU critically high at 95%.<br/>Clear cache?"
    User->>Agent: "No, ignore it."
    Agent->>LE: Log Event (cpu_spike → IGNORED)
    LE->>LE: Recalculate ignore_rate
    LE->>UP: Update: cpu_spike → MEDIUM
    
    Note over LE,UP: Next time cpu_spike occurs,<br/>alert is shown with lower urgency<br/>or suppressed entirely.
```

### Explainability & User Override Flow

```mermaid
sequenceDiagram
    participant User as 👤 User
    participant Agent as 🧠 Agent Core
    participant LC as 🔍 Learning Controls
    participant UP as 💾 User Profile

    User->>Agent: "Why don't I get cricket updates?"
    Agent->>LC: detect_learning_intent()
    LC->>UP: Fetch cricket priority data
    UP-->>LC: {priority: LOW, ignore_rate: 0.75,<br/>events: 4, ignored: 3}
    LC-->>Agent: Explanation Built
    Agent->>User: "You ignored 3 of 4 cricket alerts.<br/>Priority auto-reduced to LOW."
    
    User->>Agent: "Always notify me about cricket."
    Agent->>LC: Process Override
    LC->>UP: Set cricket → ALWAYS
    LC-->>Agent: Override Confirmed
    Agent->>User: "Done. Cricket alerts set to ALWAYS."
```

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
git clone https://github.com/Rexy-5097/raptor-ai.git
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

**[Rexy-5097](https://github.com/Rexy-5097)**

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
