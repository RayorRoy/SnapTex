# SnapTex Development Roadmap

## M0 — Repository and Baseline

### Goal

Create a stable project baseline before OCR replacement or feature expansion.

### Tasks

- [x] Initialize repository
- [x] Add core documentation
- [ ] Choose exact MathOCR baseline / reference revision
- [ ] Import or recreate desktop baseline
- [ ] Confirm development environment on Windows
- [ ] Confirm Electron renderer starts
- [ ] Confirm Python sidecar starts
- [ ] Confirm IPC / JSON-RPC communication
- [ ] Confirm tray behavior
- [ ] Confirm capture overlay behavior

### Acceptance

The desktop app starts locally and can trigger a capture workflow without packaging.

---

## M1 — Default Formula OCR

### Goal

Implement:

```text
Screenshot → PP-FormulaNet_plus-S → LaTeX
```

### Tasks

- [ ] Add FormulaEngine abstraction
- [ ] Add PaddleFormulaEngine
- [ ] Integrate PP-FormulaNet_plus-S
- [ ] Keep engine resident after load
- [ ] Return structured OCRResult
- [ ] Record inference latency
- [ ] Handle engine-not-ready state
- [ ] Add basic OCR test samples

### Acceptance

A screenshot of a formula returns LaTeX through the sidecar API.

---

## M2 — Core Snip Experience

### Goal

Deliver the first usable SnapTex workflow.

### Tasks

- [ ] Add Format Service
- [ ] Add LaTeX formatter
- [ ] Add Markdown formatter
- [ ] Add MathML formatter
- [ ] Add AsciiMath formatter
- [ ] Add latest-result cache
- [ ] Add automatic clipboard write
- [ ] Add lightweight success notification
- [ ] Register Alt+1..Alt+4 output shortcuts
- [ ] Do not open main window after recognition

### Acceptance

```text
Alt+Q
→ select formula
→ result is recognized
→ default format is copied
→ Ctrl+V works immediately
```

Alt+1..Alt+4 can switch clipboard formats without re-running OCR.

---

## M3 — Model Management and Product Basics

### Goal

Make SnapTex usable by someone who did not set up the OCR model manually.

### Tasks

- [ ] Add model metadata definition
- [ ] Add first-run model detection
- [ ] Add model download
- [ ] Add progress reporting
- [ ] Add checksum / integrity validation
- [ ] Add retry and failure handling
- [ ] Add History page
- [ ] Add Settings page
- [ ] Add configurable capture shortcut
- [ ] Add configurable output shortcuts
- [ ] Add configurable default output format
- [ ] Add notification toggle
- [ ] Add launch-at-startup option

### Acceptance

A clean installation can download the default model and enter a ready state without command-line setup.

---

## M4 — Windows v0.1 Release

### Goal

Produce a testable Windows release artifact.

### Tasks

- [ ] Package Electron application
- [ ] Package Python runtime / sidecar strategy
- [ ] Verify model path outside development environment
- [ ] Verify install / uninstall
- [ ] Verify clean first run
- [ ] Verify system tray lifecycle
- [ ] Verify multi-monitor capture
- [ ] Verify common DPI scaling configurations
- [ ] Add logs
- [ ] Add crash-safe sidecar restart
- [ ] Add release notes
- [ ] Create v0.1.0 tag

### Acceptance

A Windows user can install SnapTex, download the default model, run it from the tray, and use the capture-to-clipboard workflow.

---

# Benchmark Track

Benchmark work runs alongside M1–M4.

## Initial Dataset

Target at least 100 real screenshots:

- 20 simple formulas
- 20 fractions / roots / superscripts / subscripts
- 15 matrices / cases
- 15 multi-line equations
- 15 complex paper formulas
- 10 Chinese textbook formulas
- 5 low-quality screenshots

## Metrics

Track:

- recognition correctness
- inference time
- end-to-end time
- startup model load time
- memory footprint
- CPU usage
- failure category

## Decision Gates

If PP-FormulaNet_plus-S fails too frequently on representative screenshots, evaluate an optional higher-accuracy engine after v0.1 core workflow is stable.

---

# Later Roadmap

## v0.2

- optional high-accuracy model
- engine selection
- re-recognition
- image preprocessing
- benchmark tooling

## v0.3

- mixed text + math screenshot recognition
- Markdown-oriented capture mode

## v0.4

- Typst output
- Obsidian-friendly output
- application presets
- improved Word / WPS workflows

## v0.5

- macOS investigation
- Linux investigation
