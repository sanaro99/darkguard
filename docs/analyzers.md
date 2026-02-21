# DarkGuard Analyzers

## DOM Analyzer (`dom_analyzer/`)
- **Input**: `DomMetadata` — hidden elements, interactive elements (buttons/links with computed styles), pre-checked inputs, page URL
- **Output**: `list[Detection]` — flags visual interference (contrast/size-ratio), pre-selection, hidden elements
- **Method**: Rules engine (no LLM)

## Text Analyzer (`text_analyzer/`)
- **Input**: `TextContent` — button labels, headings, body text
- **Output**: `list[Detection]` — flags confirmshaming, urgency/scarcity, misdirection
- **Method**: Pattern matching + NLP rules

## Visual Analyzer (`visual_analyzer/`)
- **Input**: `VisualPayload` — screenshot base64 + DOM metadata
- **Pipeline**: `element_map_builder.py` converts screenshot + DOM into an `ElementMap` JSON, which is sent to an LLM for reasoning
- **Output**: `list[Detection]` — flags layout anomalies, visual interference
- **Method**: ElementMap → LLM

## Review Analyzer (`review_analyzer/`)
- **Input**: `ReviewPayload` — review text blobs
- **Output**: `list[Detection]` — flags fake social proof, burst patterns
- **Method**: LLM + heuristic rules
