# DarkGuard

A Chrome MV3 browser extension that detects dark patterns on any webpage using 4 independent AI/rules-based analyzers, with inline overlay explanations.

## Architecture

| Component | Stack |
|---|---|
| Extension | TypeScript (strict), Svelte 5, Vite, Chrome MV3 |
| Backend | Django 5, Django REST Framework, asyncio |
| AI | Google GenAI (Gemini) for visual + review analysis |

## Quick Start

### Backend

```bash
cd backend
python -m venv .venv
.venv\Scripts\activate        # Windows
pip install -r requirements.txt
cp ../.env.example .env       # edit with your keys
python manage.py runserver
```

### Extension

```bash
cd extension
npm install
npm run build
```

Then load `extension/dist/` as an unpacked extension in `chrome://extensions`.

## Analyzers

| Analyzer | Signal | Method |
|---|---|---|
| DOM | Hidden elements, size ratios, contrast, pre-checked inputs | Rules engine |
| Text | Confirmshaming, urgency, misdirection labels | NLP / rules |
| Visual | Layout anomalies, visual interference | ElementMap → LLM |
| Review | Fake social proof, burst patterns | LLM / rules |

See [docs/analyzers.md](docs/analyzers.md) for detailed schemas.
