# SnapTex Architecture

## 1. Architecture Overview

SnapTex is split into a desktop application layer and an OCR inference sidecar.

```text
┌──────────────────────────────────────┐
│ Electron Main Process                │
│                                      │
│ Shortcuts / Tray / Clipboard         │
│ Capture Manager / Model Manager      │
└─────────────────┬────────────────────┘
                  │ IPC / JSON-RPC
                  ▼
┌──────────────────────────────────────┐
│ Python OCR Sidecar                   │
│                                      │
│ Engine Interface                     │
│        ↓                             │
│ Paddle Formula Engine                │
│        ↓                             │
│ PP-FormulaNet_plus-S                 │
└──────────────────────────────────────┘
```

The renderer process is responsible for settings, history, previews, and other low-frequency UI.

## 2. Responsibilities

### Electron Main Process

Responsible for:

- application lifecycle
- global shortcuts
- system tray
- clipboard
- capture overlay coordination
- OCR sidecar lifecycle
- model availability checks
- latest-result cache
- persistent settings
- history coordination

### Renderer

Responsible for:

- settings UI
- recognition history
- formula preview
- model download state
- error presentation

### Python Sidecar

Responsible for:

- OCR model loading
- image preprocessing
- recognition
- inference timing
- health reporting

It should not own desktop UI logic.

## 3. Suggested Repository Layout

```text
SnapTex/
├── electron/
│   ├── main/
│   │   ├── main.ts
│   │   ├── shortcuts.ts
│   │   ├── clipboard.ts
│   │   ├── tray.ts
│   │   ├── notification.ts
│   │   └── model-manager.ts
│   ├── capture/
│   │   ├── overlay.ts
│   │   └── capture-manager.ts
│   └── ipc/
│       └── handlers.ts
│
├── src/
│   ├── pages/
│   │   ├── Home/
│   │   ├── History/
│   │   └── Settings/
│   └── components/
│       ├── FormulaPreview/
│       ├── ModelDownload/
│       └── HistoryItem/
│
├── engine/
│   ├── server.py
│   ├── engines/
│   │   ├── base.py
│   │   └── paddle_formula.py
│   └── model_manager.py
│
├── formatters/
│   ├── latex.ts
│   ├── markdown.ts
│   ├── mathml.ts
│   └── asciimath.ts
│
├── shared/
│   ├── types.ts
│   └── constants.ts
│
├── docs/
└── tests/
```

## 4. OCR Engine Interface

The desktop application must not depend directly on PaddleOCR.

The Python side should expose a stable abstraction such as:

```python
class FormulaEngine:
    def load(self):
        ...

    def recognize(self, image):
        ...

    def unload(self):
        ...

    def health(self):
        ...
```

Initial implementation:

```text
FormulaEngine
    └── PaddleFormulaEngine
            └── PP-FormulaNet_plus-S
```

Future engines should plug into the same interface.

## 5. OCR Result Contract

Proposed desktop-facing result:

```ts
interface OCRResult {
  latex: string
  confidence?: number
  inferenceTime: number
  engine: string
  timestamp: number
}
```

The first implementation may omit confidence if the selected engine does not provide a meaningful score.

## 6. Format Service

OCR returns one primary representation: raw LaTeX.

Format generation happens after OCR.

```ts
interface OutputFormatter {
  id: string
  name: string
  format(latex: string): Promise<string>
}
```

Initial formatters:

- LatexFormatter
- MarkdownFormatter
- MathMLFormatter
- AsciiMathFormatter

This design allows future formats such as Typst or application-specific output without changing OCR.

## 7. Latest Result Cache

Keep the most recent successful recognition in memory.

```ts
interface LastResult {
  latex: string
  markdown: string
  mathml: string
  asciimath: string
  imagePath?: string
  createdAt: number
}
```

This allows Alt+1 through Alt+4 to update the clipboard without re-running OCR.

## 8. Model Lifecycle

The default model should not be embedded in the installer.

Startup logic:

```text
App start
→ check model path
→ if missing, show first-run download UI
→ validate downloaded model
→ start Python sidecar
→ load model
→ report engine ready
```

Once loaded, the model remains resident until application shutdown or explicit engine restart.

## 9. Capture Flow

```text
Global hotkey
→ create transparent capture overlay
→ drag-select region
→ capture selected pixels
→ close overlay
→ send image to OCR Manager
```

Capture requirements:

- Escape cancellation
- multi-monitor support
- DPI scaling awareness
- minimal visual delay
- no main-window focus requirement

## 10. Recognition Flow

```text
CaptureResult
→ OCRManager.recognize()
→ Python sidecar
→ FormulaEngine.recognize()
→ OCRResult
→ FormatService.generate()
→ LastResult
→ clipboard.writeText(default format)
→ lightweight success toast
```

The main window should not open during this flow.

## 11. Persistence

Suggested separation:

### Settings

Persist:

- capture shortcut
- output shortcuts
- default output format
- startup behavior
- notification preference
- model path/version

### History

Persist at minimum:

- id
- raw LaTeX
- timestamp
- engine
- optional screenshot path

Derived formats do not have to be stored because they can be regenerated.

## 12. Error Handling

Errors should be classified rather than surfaced as generic failures.

Suggested categories:

- capture_failed
- engine_not_ready
- model_missing
- model_download_failed
- inference_failed
- empty_result
- clipboard_failed
- formatter_failed

High-frequency errors should use lightweight notifications instead of forcing open the main window.

## 13. Security and Privacy

Default design:

- screenshots remain local
- OCR inference remains local
- no account required
- no telemetry required for core functionality

Any future network feature should be opt-in and documented separately.
