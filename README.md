# SnapTex

**SnapTex** is a local-first screenshot-to-LaTeX desktop tool focused on a fast Mathpix Snip-like workflow:

```text
Hotkey → select formula → OCR → auto-copy → paste
```

## v0.1 Goal

The first release targets Windows and focuses on one thing: make formula capture feel instant.

- Global screenshot hotkey
- Region capture
- Local formula OCR
- PP-FormulaNet_plus-S as the default model
- Automatic clipboard copy after recognition
- Four output formats: LaTeX, Markdown, MathML, AsciiMath
- Quick format switching for the most recent result
- System tray
- History and basic settings
- Offline use after the default model is downloaded

## Planned Architecture

```text
Electron + React + TypeScript
            │
        Capture Layer
            │
        OCR Manager
            │
      Python Sidecar
            │
 PP-FormulaNet_plus-S
            │
          LaTeX
            │
       Format Service
   ┌────────┼────────┐
 LaTeX   Markdown   MathML   AsciiMath
            │
        Clipboard
```

## Documentation

- [Technical Roadmap](docs/TECHNICAL_ROADMAP.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Development Roadmap](docs/ROADMAP.md)

## Status

SnapTex is currently in the design / v0.1 bootstrap stage.

## Repository

```bash
git clone git@github.com:RayorRoy/SnapTex.git
cd SnapTex
```
