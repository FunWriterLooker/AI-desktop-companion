# AI-desktop-companion
Local, privacy-focused AI desktop companion which so far has offline chat, hardware monitoring and mood-reactive UI, built from scratch in Python.


Local AI Desktop Companion

A privacy-focused AI companion that runs entirely on your machine. No cloud
calls, no accounts, just a local LLM (via Ollama) wrapped in a floating,
customizable desktop UI.

**Status: active development.** Core features are working and stable;
see [Roadmap](#roadmap) for what's next.

## Why

Most AI assistants require sending your conversations to someone else's
server. Bob started as a silly idea a fully local alternative which is still in early development

## Features

- **Fully offline chat** via a local Ollama backend
- **Editable personality system:** swap Bob's personality via plain text
  files, with persistent conversation history per personality
- **Floating UI** (PySide6): frameless, transparent, always-on-top window,
  toggle between bubble and full-chat view, shrink to tray icon, idle-timer
  auto-shrink
- **Hardware watchdog:** live CPU/RAM/disk/GPU/VRAM/temp monitoring with
  rolling averages, cooldown-gated alerts and stat
  injection into Bob's own context (so Bob can reason about system state)
- **Mood system:** mood-reactive images with synonym mapping and graceful
  fallbacks
- **Model picker:** with background caching so switching models doesn't
  freeze the UI
- **Decent working Ollama error handling:** surfaces actual backend errors instead
  of failing silently


## Roadmap

**Near-term**
- Transparent mood art
- Persisting settings
- Saved / selectable chat history

**Bigger pieces**
- Memory system (dedicated build session)
- `.exe` packaging (with PySide6 LGPL attribution)

**Phase 3: "Fun" capabilities**
- Text-to-speech
- Text-to-image for dynamic mood faces (FLUX.1-dev is a candidate, deferred
  due to ~24GB download size, HF licensing and VRAM offload requirements)
- Speech-to-text
- Vision models

**Backend generalization**
Model-talking logic is intentionally sealed inside `OllamaWorker`. No
generalization to an OpenAI-compatible API until there's a concrete second
backend to test against, avoiding premature abstraction.

## Engineering notes

A few habits/lessons that shaped the build:

- **Never block the main thread.** Hit this twice (chat calls, model list
  fetches), both fixed with `QThread` + `Signal`. Now a standing rule.
- **Judgment lives in code, not prompts.** Mood synonym mapping, temperature
  calibration labels and honest sensor-limitation notes are handled in
  logic rather than baked into the model's instructions.
- **Cache rarely-changing data with a manual refresh escape hatch:** Used
  for the model list.
- **Surface raw errors before guessing at fixes.** A single debug print of
  Ollama's raw response once revealed a missing `llama-server` binary that
  other debugging approaches missed.
- **One runnable checkpoint before moving on:** Kept the build stable at
  every step instead of stacking unverified changes.


## Project structure

```
companion_gui.py     # main application / UI
watchdog.py           # hardware monitoring module
personalities/         # editable personality text files
moods/                 # mood-reactive image assets
ROADMAP.md             # living doc of shipped + planned work
```

## Screenshot

![Bob bubble UI](bob.png)

*Bob in "bubble" mode — floating, transparent, mood-reactive.*

## Acknowledgments

Built with development assistance from Claude
used as a programming assistant and debugging aid throughout the project.
