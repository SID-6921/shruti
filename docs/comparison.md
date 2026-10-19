# How we compared Shruti with other models (Udgam Labs, 18-19 October 2026)

Every model below was run by us on the same test clips, in the same order, with the same audio decoding, the same text
normaliser and the same scorer. Word error rate (WER, %) is lower-is-better. Monsoon Hindi uses the official OIWER
lattice scorer of the Hugging Face Open ASR leaderboard, on the public split.

## Test sets
| Set | What it is | Clips |
|---|---|---|
| Monsoon Hindi | read and spontaneous Hindi, the HF Hindi leaderboard's test set (public split) | 753 |
| FLEURS Hindi | read Wikipedia sentences (Google FLEURS, test) | 418 |
| Lahaja | Hindi from speakers of many Indian regions (AI4Bharat) | 6,152 |
| MUCS Hinglish | Hindi-English code-mixed lectures (MUCS 2021); references write most English words in Latin script | 3,136 |
| Svarah | Indian-accented English (AI4Bharat) | 6,656 |
| LibriSpeech clean | US read English | 2,620 |
| Silence | 100 clips of digital silence; we count the words a model invents | 100 |

## Results
| Model | File size | Monsoon Hindi | FLEURS Hindi | Lahaja | MUCS Hinglish | Svarah | LibriSpeech | Words invented on silence |
|---|---|---|---|---|---|---|---|---|
| **Shruti** | **29.5 MB** | 20.46 | 19.92 | 26.28 | **35.21** | 22.47 | **5.95** | 0 |
| Koyal-hi-120M | 481 MB | **11.64** | **9.56** | 18.70 | 57.51 | Hindi only | Hindi only | 0 |
| IndicConformer Hindi (fp32 ONNX) | 482 MB | 15.31 | 11.31 | **13.18** | 64.48 | Hindi only | Hindi only | 88 |
| Vosk small (hi + en-in) | 139 MB | 35.75 | 18.07 | 26.60 | 61.14 | 38.51 | 30.18 | 1 |
| Whisper-Hindi2Hinglish-Swift | 296 MB | 57.16 * | n/c | n/c | n/c | **20.16** | 12.60 | 1,676 |
| Whisper-small | 971 MB | 128.25 | 59.42 | 117.5 | – | – | – | – |
| whisper-base-hinglish (fp32) | 290 MB | 65.66 * | – | – | 87.05 * (43.45 romanised) | 27.11 † | 15.53 † | 500 |
| whisper-tiny-hinglish-v2 (fp32) | 151 MB | 70.60 * | – | – | 87.73 * (44.94 romanised) | 34.50 † | 23.92 † | 500 |

\* The model writes Roman script only, so this mostly measures the script. n/c: not comparable for the same reason.
† First 1,500 Svarah and first 1,000 LibriSpeech clips; Shruti on the same clips: 21.35 and 6.36.
Romanised: reference and output both converted to Roman script by the same function; Shruti scores 34.32 that way.
The whisper-*-hinglish model cards report 28.4 and 32.0 on a 250-clip sample with their author's own converter, which we
did not run. –: not measured.

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

The other models ran on an NVIDIA RTX 4090 or on the Mac; Shruti ran on the Mac in its on-device engine. A repeat run of
200 MUCS clips gave identical outputs for every model. Other files in this folder: `shruti_on_device.json` (Shruti's
numbers), `phone_benchmark.json` (speed and memory on a Samsung Galaxy F62).
