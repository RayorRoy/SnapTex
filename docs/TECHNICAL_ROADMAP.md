# SnapTex Technical Roadmap

## 1. Project Goal

SnapTex is a local-first desktop tool for converting screenshot-selected mathematical formulas into copy-ready text formats.

The primary user workflow is:

```text
Global hotkey
→ drag to select a formula
→ OCR runs locally
→ default format is copied automatically
→ paste with Ctrl+V
```

The v0.1 goal is not to become a full document OCR suite. It is to make formula capture fast, reliable, and frictionless.

## 2. v0.1 Scope

### Must Have

- Windows desktop application
- System tray
- Global screenshot hotkey
- Region capture overlay
- Local formula OCR
- PP-FormulaNet_plus-S as the default model
- Automatic clipboard copy after successful recognition
- Four output formats:
  - LaTeX
  - Markdown
  - MathML
  - AsciiMath
- Quick switching for the most recent result
- Recognition history
- Basic settings
- First-run model download

### Not in v0.1

- PDF-to-Markdown
- General-purpose OCR
- Full-page document parsing
- Table OCR
- Cloud OCR
- User accounts
- Cloud sync
- Plugin marketplace
- LLM-based formula correction
- macOS
- Linux

## 3. Core Design Principles

### Capture First

The product should feel like a screenshot utility first and an AI application second.

### Local First

Recognition should run locally after the model is downloaded.

### Engine Agnostic

The desktop app must not depend directly on one OCR engine.

### Fast by Default

The default experience should require no model or inference knowledge from the user.

## 4. Proposed Technology Stack

### Desktop

- Electron
- React
- TypeScript

### OCR Sidecar

- Python
- PaddleOCR formula recognition
- PP-FormulaNet_plus-S

### Output Conversion

A separate Format Service converts the OCR result into:

- LaTeX
- Markdown
- MathML
- AsciiMath

OCR should run once per screenshot. Output format conversion should not trigger additional model inference.

## 5. Model Strategy

v0.1 ships with support for one default model:

```text
PP-FormulaNet_plus-S
```

The model should be downloaded on first run instead of being embedded in the application installer.

Future optional engines may include:

- PP-FormulaNet_plus-M
- UniMERNet
- Mixed-content OCR engines

## 6. Runtime Model Strategy

Do not load the model for every screenshot.

Preferred lifecycle:

```text
App starts
→ Python sidecar starts
→ OCR model loads once
→ model remains resident
→ screenshots are sent to the running engine
```

This makes repeated screenshot recognition much faster.

## 7. Core Data Flow

```text
Global Hotkey
    ↓
Capture Overlay
    ↓
Screenshot Image
    ↓
OCR Manager
    ↓
Python Sidecar
    ↓
PP-FormulaNet_plus-S
    ↓
Raw LaTeX
    ↓
Format Service
    ↓
LaTeX / Markdown / MathML / AsciiMath
    ↓
Last Result Cache
    ↓
Clipboard
    ↓
Toast Notification
```

## 8. Default Shortcuts

Initial proposed defaults:

```text
Alt + Q  → Capture formula
Alt + 1  → Copy latest LaTeX
Alt + 2  → Copy latest Markdown
Alt + 3  → Copy latest MathML
Alt + 4  → Copy latest AsciiMath
```

All shortcuts should later be configurable.

## 9. Performance Targets

These are product targets rather than guaranteed benchmark results.

- Capture overlay should feel immediate
- Typical formula OCR target: under 1 second on a normal development machine
- Output conversion: near-instant
- Clipboard update: near-instant
- Switching among cached formats: near-instant

Real targets should be updated after benchmarking on representative Windows machines.

## 10. Benchmark Plan

Build an internal formula screenshot dataset covering at least:

- Simple single-line formulas
- Fractions, roots, subscripts, superscripts
- Matrices and cases
- Multi-line equations
- Complex research-paper formulas
- Chinese textbook formulas
- Low-resolution / imperfect screenshots

For each sample record:

- Recognition correctness
- OCR inference time
- End-to-end latency
- CPU usage
- Peak memory
- Model load time
- Failure category

## 11. Milestones

### M0 — Baseline

Establish the desktop application baseline and confirm:

- Electron
- React
- Python sidecar
- screenshot capture
- tray
- local communication

### M1 — Formula OCR

Integrate PP-FormulaNet_plus-S:

```text
Screenshot → OCR → LaTeX
```

### M2 — Snip Workflow

Implement:

```text
Screenshot
→ OCR
→ four formats
→ auto-copy
→ toast
```

Also implement latest-result format shortcuts.

### M3 — Product Basics

Add:

- first-run model download
- download progress
- model validation
- history
- settings
- configurable shortcuts
- configurable default output format

### M4 — Windows v0.1

Finish:

- installer
- tray behavior
- startup behavior
- error recovery
- logs
- packaging
- release checklist

## 12. v0.1 Success Criteria

The release is successful when this workflow is reliable:

```text
Open a paper
→ press Alt+Q
→ select a formula
→ release mouse
→ LaTeX is copied automatically
→ press Ctrl+V
```

If another format is needed:

```text
Alt+2 / Alt+3 / Alt+4
→ Ctrl+V
```

No additional screenshot or OCR pass should be required.
