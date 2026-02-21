# 🛡️ DarkGuard

> A Chrome MV3 browser extension that detects dark patterns on any webpage using four independent AI/rules-based analyzers, with inline overlay explanations.

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](LICENSE)
[![Python 3.12+](https://img.shields.io/badge/Python-3.12+-3776AB.svg)](https://python.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178C6.svg)](https://typescriptlang.org)

---

## Overview

DarkGuard combines a **Chrome extension** (content scripts + service worker) with a **Django REST backend** that runs four standalone analyzers concurrently. Each analyzer inspects a different signal — DOM metadata, visible text, page layout, and review content — then results are merged, deduplicated, and displayed as inline overlays directly on the webpage.

### Key Capabilities

- 🔍 **Four independent analyzers** running in parallel via `asyncio.gather()`
- 🧹 **PII sanitization** in-browser before any data leaves (emails, phones, SSNs stripped)
- 🎯 **Corroborated detection** flag when 2+ analyzers agree on the same element & category
- 💬 **Inline overlays** via Shadow DOM with severity-coded borders and hover tooltips
- 📊 **Popup dashboard** showing scan results, counts, and confidence scores
- 🔒 **CORS locked** to `chrome-extension://*` origins (no wide-open dev config)

## Architecture at a Glance

```
┌──────────────────────────────┐     POST /api/analyze     ┌──────────────────────────┐
│     Chrome Extension         │ ──────────────────────── ▶ │     Django Backend        │
│                              │                            │                          │
│  ┌─────────┐  ┌───────────┐ │                            │  ┌───────────────────┐   │
│  │Collector │→ │ Sanitizer │ │                            │  │    Dispatcher      │   │
│  └─────────┘  └───────────┘ │     JSON response          │  │  ┌──────────────┐  │   │
│  ┌──────────┐               │ ◀ ──────────────────────── │  │  │ DOM Analyzer  │  │   │
│  │Screenshot│               │                            │  │  │ Text Analyzer │  │   │
│  └──────────┘               │                            │  │  │Visual Analyzer│  │   │
│  ┌─────────┐  ┌──────────┐ │                            │  │  │Review Analyzer│  │   │
│  │ Overlay  │← │  Popup   │ │                            │  │  └──────────────┘  │   │
│  └─────────┘  └──────────┘ │                            │  └───────────────────┘   │
└──────────────────────────────┘                            └──────────────────────────┘
```

> 📐 For detailed diagrams see **[docs/architecture.md](docs/architecture.md)** and **[docs/data-flow.md](docs/data-flow.md)**.

## Quick Start

### Prerequisites

- Python 3.12+
- Node.js 20+
- Google Chrome

### Backend

```bash
cd backend
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # macOS / Linux
pip install -r requirements.txt
cp ../.env.example .env         # edit with your keys
python manage.py runserver
```

### Extension

```bash
cd extension
npm install
npm run build
```

Then load `extension/dist/` as an unpacked extension in `chrome://extensions` (enable Developer Mode).

## Project Structure

```
darkguard/
├── extension/               # Chrome MV3 extension (→ extension/README.md)
│   ├── src/
│   │   ├── background/      # Service worker orchestrator
│   │   ├── content/          # Collector, sanitizer, overlay renderer
│   │   ├── popup/            # Svelte 5 popup UI
│   │   ├── types/            # Shared TypeScript interfaces
│   │   └── utils/            # API client, screenshot capture
│   └── package.json
├── backend/                  # Django REST backend (→ backend/README.md)
│   ├── core/                 # Dispatcher, base interfaces, models
│   ├── dom_analyzer/         # Rules engine for DOM patterns
│   ├── text_analyzer/        # Regex/NLP for text patterns
│   ├── visual_analyzer/      # ElementMap → LLM reasoning
│   ├── review_analyzer/      # Fake review detection
│   └── requirements.txt
├── docs/                     # Documentation & diagrams
│   ├── architecture.md       # System architecture diagrams
│   ├── data-flow.md          # Data flow diagrams
│   ├── analyzers.md          # Analyzer deep-dive
│   ├── privacy.md            # PII sanitization docs
│   └── api.md                # API reference
├── .env.example              # Environment variable template
├── LICENSE                   # GNU GPL v3
└── README.md                 # ← You are here
```

## Analyzers

| Analyzer | Signal | Method | Dark Patterns Detected |
|---|---|---|---|
| **DOM** | Hidden elements, size ratios, contrast, pre-checked inputs | Rules engine | Pre-selection, Visual Interference |
| **Text** | Button labels, headings, body text | Regex / NLP | Confirmshaming, Urgency/Scarcity, Misdirection |
| **Visual** | Page layout (ElementMap from DOM metadata) | ElementMap → LLM | Visual Interference, Misdirection |
| **Review** | Review text blobs | Heuristics + LLM | Fake Social Proof |

> 📖 Deep dive: **[docs/analyzers.md](docs/analyzers.md)**

## Documentation

| Document | Description |
|---|---|
| [docs/architecture.md](docs/architecture.md) | System architecture with Mermaid diagrams |
| [docs/data-flow.md](docs/data-flow.md) | End-to-end data flow walkthrough |
| [docs/analyzers.md](docs/analyzers.md) | Per-analyzer input/output/method specs |
| [docs/privacy.md](docs/privacy.md) | PII sanitization strategy |
| [docs/api.md](docs/api.md) | REST API reference |
| [extension/README.md](extension/README.md) | Extension architecture & build guide |
| [backend/README.md](backend/README.md) | Backend architecture & dev guide |

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feat/my-feature`)
3. Commit changes (`git commit -m "feat: add my feature"`)
4. Push to the branch (`git push origin feat/my-feature`)
5. Open a Pull Request

Please ensure:
- TypeScript strict mode (no `any` types) for extension code
- PEP 8 + type hints for Python code
- Each analyzer module stays standalone — never mix concerns across analyzers
- PII must be sanitized before any data leaves the browser

## License

This project is licensed under the **GNU General Public License v3.0** — see [LICENSE](LICENSE) for details.
