# Release notes

## 1.0.0 (19 October 2026)

First public release of Shruti, speech-to-text for Hindi, English and Hinglish. The weights are on Hugging Face
([udgamlabs/Shruti](https://huggingface.co/udgamlabs/Shruti)); this repository adds runnable examples, the full
comparison and checksums.

- **Model:** 55M parameters, built on Cactus Whistle (Apache-2.0) with all weights retrained on 2,732 hours of openly
  licensed Indian speech and 714 added Devanagari vocabulary pieces. Hindi in Devanagari, English words in Latin script.
- **Files:** `shruti.cact` (29.5 MB, 4-bit weights, for the Cactus needle engine) and `shruti.safetensors` (222 MB,
  full-precision weights). Checksums in `SHA256SUMS`.
- **Runs offline:** Python (`cactus-needle`), the `needle` command line, and the browser (WebAssembly).
- **Results** (word error rate, same harness for every model): MUCS Hinglish 35.21, the lowest of the open models we
  measured; Monsoon Hindi 20.46; LibriSpeech 5.95; 0 words invented on 100 silent clips. See `docs/comparison.md`.
- **Examples:** `examples/transcribe.py`, `examples/transcribe.sh` and the `examples/browser/` page.

Licence: Apache-2.0. Known limitations are listed in the README (pure Hindi behind larger Hindi-only models; Indian
English behind a 10× larger model; unseen names spelt by sound; noise and music not tested).
