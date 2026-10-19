# How we compared Shruti with other models (Udgam Labs, 18-19 October 2026)

Every model below was run by us on the same test clips, in the same order, with the same audio decoding, the same text
normaliser and the same scorer. Word error rate (WER, %) is lower-is-better. Monsoon Hindi uses the official OIWER
lattice scorer of the Hugging Face Open ASR leaderboard, on the public split. The technical report describes the
harness and every result in full: [udgamlabs.com/research/shruti](https://udgamlabs.com/research/shruti).

## Test sets
| Set | What it is | Clips |
|---|---|---|
| Monsoon Hindi | read and spontaneous Hindi, the HF Hindi leaderboard's test set (public split) | 753 |
| FLEURS Hindi | read Wikipedia sentences (Google FLEURS, test) | 418 |
| Lahaja | Hindi from speakers of many Indian regions (AI4Bharat) | 6,152 |
| MUCS Hinglish | Hindi-English code-mixed lectures (MUCS 2021); references write most English words in Latin script | 3,136 |
| Svarah | Indian-accented English (AI4Bharat) | 6,656 |
| LibriSpeech clean | US read English | 2,620 |
| Silence | 100 four-second clips: 50 of digital silence and 50 of very faint synthetic hiss; we count the words a model invents | 100 |

## Results
| Model | File size | Monsoon Hindi | FLEURS Hindi | Lahaja | MUCS Hinglish | Svarah | LibriSpeech | Words invented on silence |
|---|---|---|---|---|---|---|---|---|
| **Shruti** | **29.5 MB** | 20.46 | 19.92 | 26.28 | **35.21** | 22.47 | 5.95 | 0 |
| Koyal-hi-120M | 481 MB | **11.64** | **9.56** | 18.70 | 57.51 | Hindi only | Hindi only | 0 |
| IndicConformer Hindi (fp32 ONNX) | 482 MB | 15.31 | 11.31 | **13.18** | 64.48 | Hindi only | Hindi only | 88 |
| Vosk small (hi + en-in) | 139 MB | 35.75 | 18.07 | 26.60 | 61.14 | 38.51 | 30.18 | 1 |
| Whisper-Hindi2Hinglish-Swift | 296 MB | 57.16 * | n/c | n/c | n/c | 20.16 | 12.60 | 1,676 |
| Whisper-small | 971 MB | 128.25 | 59.42 | 117.5 | – | – | – | – |
| whisper-base-hinglish (fp32) | 290 MB | 65.66 * | – | – | 87.05 * (43.45 romanised) | 27.11 † | 15.53 † | 500 |
| whisper-tiny-hinglish-v2 (fp32) | 151 MB | 70.60 * | – | – | 87.73 * (44.94 romanised) | 34.50 † | 23.92 † | 500 |
| Whisper large-v3 (fp16) | 3,087 MB | 33.29 | 28.88 | 31.19 ‡ | 79.94 ‡ | **7.39** ‡ | **2.25** ‡ | 538 |

\* The model writes Roman script only, so this mostly measures the script. n/c: not comparable for the same reason.
† First 1,500 Svarah and first 1,000 LibriSpeech clips; Shruti on the same clips: 21.35 and 6.36.
‡ First 300 Lahaja, Svarah and LibriSpeech clips and first 600 MUCS clips; Shruti on the same clips: 22.87, 21.35, 5.76
and 45.3. On Monsoon and FLEURS, Whisper large-v3 ran on every clip; in that run Shruti scored 20.46 and 20.14 (FLEURS
with the newest engine build; 19.92 in the table is the build of 18 October).
Romanised: reference and output both converted to Roman script by the same function; Shruti scores 34.32 that way.
The whisper-*-hinglish model cards report 28.4 and 32.0 on a 250-clip sample with their author's own converter, which we
did not run. –: not measured.

## Whisper large-v3 on the same clips
Both models on the Mac, identical clips. Whisper minus Shruti in points, with a paired-bootstrap 95% interval over clips
(the Monsoon lattice scorer has none). Negative = Whisper better.

| Set (clips) | Shruti | Whisper large-v3 | Whisper minus Shruti (95% interval) |
|---|---|---|---|
| Monsoon Hindi, OIWER (753) | **20.46** | 33.29 | +12.8 (lattice: no interval) |
| FLEURS Hindi (418) | **20.14** | 28.88 | +8.7 (+6.0 to +12.5) |
| Lahaja, Hindi accents (first 300) | **22.87** | 31.19 | +8.3 (+4.4 to +14.7) |
| MUCS Hinglish (first 600) | **45.3** | 79.94 | +34.6 (+23.5 to +48.7); partly script (Whisper writes many English words in Devanagari) |
| Svarah, Indian English (first 300) | 21.35 | **7.39** | -14.0 (-17.1 to -11.2) |
| LibriSpeech clean (first 300) | 5.76 | **2.25** | -3.5 (-4.3 to -2.8) |
| Silence, words invented (100) | **0** | 538 | |

Whisper ran with plain beam search, without its temperature fallback, which causes some repetition loops. With every
clip removed where any model's output is more than 3 times the reference length, Shruti still leads on Hindi: FLEURS
19.9 vs 26.43 (2 clips removed), Lahaja 22.87 vs 28.66 (1 clip), MUCS 44.94 vs 58.79 (21 clips). Whisper also writes
numbers as digits (83 words with digits on FLEURS, 45 on Lahaja; Shruti 1 and 0), which the spelled-out references
count as errors. The leaderboard's own Whisper large-v3 Monsoon number (28.17, its decoding) is also behind Shruti's
20.46. A Whisper large-v3-turbo run was started but not completed; no numbers are reported for it.

## How each model was run
| Model | Exact version | Licence | Decoding |
|---|---|---|---|
| Shruti | `shruti.cact` (sha256 b189e954...) | Apache-2.0 | Cactus needle engine on a Mac, default 5 beams, language set per test |
| Koyal-hi-120M | adalat-ai/koyal-hi-120m-1.0 @ 7f2cb57 | CC-BY-4.0 (gated) | model default (greedy); writes punctuation and digits, which our normaliser counts against it where references spell numbers out |
| IndicConformer Hindi | OpenVoiceOS/ai4bharat-indicconformer-hi-onnx @ 8960b86 | MIT | greedy CTC (onnx-asr 0.12.0) |
| Vosk small | vosk-model-small-hi-0.22 + vosk-model-small-en-in-0.4 | Apache-2.0 | Kaldi defaults (vosk 0.3.44) |
| Whisper-Hindi2Hinglish-Swift | Oriserve/Whisper-Hindi2Hinglish-Swift @ cd0d4d0 | Apache-2.0 | beam 5, language=en (as its card says) |
| Whisper-small | openai/whisper-small @ 973afd2 | Apache-2.0 | beam 5, language forced (hi / en) |
| whisper-base-hinglish | abhishekgautamm/whisper-base-hinglish @ c780320 | MIT | beam 5, language=en; on MUCS also with its card's repetition settings (shown) |
| whisper-tiny-hinglish-v2 | abhishekgautamm/whisper-tiny-hinglish-v2 @ 751d719 | MIT | as above |
| Whisper large-v3 | openai/whisper-large-v3 @ 06f233f (1.55B parameters, model.safetensors 3,087 MB) | Apache-2.0 | transformers 5.18.0 on the Mac GPU (PyTorch MPS, fp16), beam 5, language forced (hi / en), no temperature fallback; clips over 30 s with sequential long-form decoding |

The other models ran on an NVIDIA RTX 4090 or on the Mac; Shruti ran on the Mac in its on-device engine. A repeat run of
200 MUCS clips gave identical outputs for every model. Other files in this folder: `shruti_on_device.json` (Shruti's
numbers), `phone_benchmark.json` (speed and memory on a Samsung Galaxy F62).
