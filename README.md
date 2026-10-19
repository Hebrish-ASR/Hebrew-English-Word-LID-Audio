# Hebrew-English-Word-LID-Audio

![Status: work in progress](https://img.shields.io/badge/status-work%20in%20progress-orange)
![Stage: planning](https://img.shields.io/badge/stage-planning-lightgrey)
![Licence: MIT](https://img.shields.io/badge/licence-MIT-blue)

**Word-level language identification in speech: for each spoken word in a
code-switched English-Hebrew recording, decide whether it is English or Hebrew, and
give its timestamps.**

This repository tracks the project's lifecycle. **No code or model yet; to be
continued.**

## Where this sits

This is the audio sibling of a text model that is already finished.

| Component | Status |
| --- | --- |
| **Text classifier:** Hebrew words in Latin letters inside English text | ✅ **Released 2026-10-15.** [`danielrosehill/hebrew-latin-token-classifier`](https://huggingface.co/danielrosehill/hebrew-latin-token-classifier) has span F1 0.941 on unseen terms. Code is in [Hebrew-Latin-Token-Classifier](https://github.com/Hebrish-ASR/Hebrew-Latin-Token-Classifier) |
| **Text corpus** | ✅ Released: [`Hebrish/hebrew-latin-code-switching`](https://huggingface.co/datasets/Hebrish/hebrew-latin-code-switching) |
| **Audio data:** read-aloud code-switched sentences | ✅ Exists: [`danielrosehill/English-Hebrew-Codeswitching-Sentences`](https://huggingface.co/datasets/danielrosehill/English-Hebrew-Codeswitching-Sentences) (512 phone takes, still growing) and [`Hebrish/English-Hebrew-Mixed-Sentences`](https://huggingface.co/datasets/Hebrish/English-Hebrew-Mixed-Sentences) (516 older recordings) |
| **Word-aligned language labels** for that audio | ⏳ Next. See the plan below |
| **Audio word-LID model** | ⏳ Planned |

## Why

The text classifier finds the Hebrew in a script. That is enough for text-to-speech,
which starts from text: detect the span, convert it to Hebrew script, and send it to
the TTS engine with the Hebrew language identifier. That pipeline is being built for
[My Weird Prompts](https://myweirdprompts.com), a podcast whose episodes often contain
Hebrew words that have long been mispronounced.

Some jobs start from audio instead:

- **TTS quality control.** Listen to a rendered episode and check whether each Hebrew
  word was actually spoken in Hebrew.
- **Speech-recognition routing.** Send Hebrew stretches of mixed speech to a Hebrew
  model, such as [ivrit.ai](https://huggingface.co/ivrit-ai)'s, and English stretches
  to an English one.
- **A public resource.** No word-level, timestamped Hebrew-English code-switching
  speech data was found anywhere in the 2026-09-22 and 2026-10-15 surveys
  ([prior art](https://github.com/danielrosehill/Hebrew-Small-Models-Planning/blob/main/docs/prior-art.md)).

## Plan

**1. Dataset first.** Build word-aligned labels for the existing recordings:

- Force-align each transcript to its audio to get per-word start and end times.
- Label each word `en` or `he` with the released text classifier.
- Spot-check the labels by ear.

The result is a timestamped English/Hebrew word-label dataset of about 1,000 clips,
which would be new in its own right.

**2. Model.** Train a small classification head on a pretrained speech encoder
(XLS-R / wav2vec2 or a Whisper encoder) on frame-level `en` / `he` / silence labels,
then pool them to words. Split by term, as the text project does, so the score
measures generalisation to unseen Hebrew words.

**3. Evaluate honestly.** Every recording so far is **one speaker** (Irish-accented
English, living in Jerusalem). A model trained on it identifies Hebrew *in that voice*.
Generalising to other speakers needs more voices, and the dataset card must say so.

## Open questions

- Which aligner handles romanized Hebrew words in English transcripts? Their
  "spelling" is not English phonetics.
- Should place names and person names get a third label, or follow the text policy?
  (Places are negative there; person names are unruled.)
- Should the detector run on raw audio, or on top of ASR output with timestamps?

## Related

- [Hebrew-Latin-Token-Classifier](https://github.com/Hebrish-ASR/Hebrew-Latin-Token-Classifier):
  the text model, with its annotation policy and taxonomy.
- [Hebrew-Small-Models-Planning](https://github.com/danielrosehill/Hebrew-Small-Models-Planning):
  the umbrella plan and the prior-art survey.
- The read-aloud audio is captured with a phone app built for a personal Parakeet
  ASR fine-tune (private repo), and published to the Hugging Face datasets above.

## Licence

MIT.
