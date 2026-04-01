# AutoEIT — GSoC 2026 Evaluation Test
**HumanAI Foundation | Mentors: Prof. Mandy Faretta-Stutenberg (NIU), Prof. Xabier Granja (Univ. of Alabama)**

Evaluation test submission for the [AutoEIT GSoC 2026 project](https://humanai.foundation/gsoc/projects/2026/project_AutoEIT.html). Covers both test tasks: audio transcription of Spanish EIT recordings (Test I) and automated meaning-based scoring of transcriptions (Test II).

---

## Repository Structure

| File | Description |
|------|-------------|
| `test1_transcription.ipynb` | Jupyter notebook: ASR pipeline using OpenAI Whisper |
| `test1_transcription.pdf` | PDF export with full cell output |
| `test1_transcription.html` | HTML export with full cell output |
| `test2_scoring.ipynb` | Jupyter notebook: automated scoring using Ortega (2000) rubric |
| `test2_scoring.pdf` | PDF export with full cell output |
| `test2_scoring.html` | HTML export with full cell output |
| `requirements.txt` | Python dependencies |

---

## Test I: Audio Transcription

### Objective
Generate transcriptions of 4 Spanish EIT participant audio files (30 sentences per participant), preserving exact learner production including disfluencies. Correct only ASR errors, not participant grammar or vocabulary errors.

### Tool Selection
**OpenAI Whisper (base model)** was selected for its strong multilingual support, open-source availability, and ability to preserve non-native speaker production without auto-correcting learner errors. Commercial ASR tools (Google Speech, Azure) were ruled out because they normalize toward native-speaker norms, which is the opposite of what EIT research requires.

### Methodology
- Language explicitly locked to Spanish (`language="es"`) to prevent cross-language hallucination
- First 150 seconds of each recording skipped via audio slicing to bypass English instructions and practice sentences
- Initial prompt set to `"Elicited imitation task in Spanish."` to anchor Whisper to the task domain
- Transcriptions preserve exact learner production: disfluencies, partial repetitions, phonological errors retained
- ASR errors (mishearing of clear speech) corrected; learner grammar/vocabulary errors preserved

### Output
30 transcribed sentences per participant written to the standard AutoEIT Excel format, one row per stimulus item.

### Known Limitations
- **Segment merging:** Whisper's VAD occasionally merges consecutive responses during silent listening intervals, producing fewer than 30 segments for some participants. A production system would use WhisperX or stable-ts for precise forced alignment.
- **Hallucination:** On very short or silent segments, Whisper occasionally generates plausible-sounding but fabricated text.
- **Model size:** The base model was used for speed. The medium or large-v3 model would yield substantially better accuracy on accented L2 speech in production.
- **L2 speech challenge:** Phonological transfer effects from L1 produce speech patterns that smaller Whisper models handle inconsistently.

### Evaluation
Output evaluated by comparing Whisper transcriptions against the provided stimulus sentences, checking for semantic preservation and Spanish word recognition accuracy across 4 participants (~120 utterances total).

---

## Test II: Automated Scoring

### Objective
Implement a reproducible script that applies the Ortega (2000) meaning-based rubric to EIT transcriptions, outputting sentence-level scores (0–4) for each utterance.

### Scoring Rubric (Ortega, 2000)
| Score | Criteria |
|-------|----------|
| 4 | Exact or near-exact repetition (accent variants accepted) |
| 3 | Full meaning preserved, minor grammatical changes acceptable |
| 2 | More than half of meaning preserved, some inexactness |
| 1 | About half of meaning, significant information missing |
| 0 | No response, gibberish, or only 1–2 words |

### Methodology
The scoring engine applies four layers in sequence:

**Layer 1 — Exact Match (Score 4)**
Unicode NFD normalization strips accent marks, then a normalized string comparison checks for perfect or accent-variant repetitions deterministically.

**Layer 2 — Semantic Similarity (Scores 3–1)**
Multilingual sentence embeddings (`paraphrase-multilingual-MiniLM-L12-v2`) compute cosine similarity between stimulus and transcription embeddings. This captures meaning-preserving variations such as synonym substitution, word reordering, and morphological changes that the rubric explicitly permits at score 3.

**Layer 3 — Word Overlap Ratio (Scores 3–1, fallback)**
A normalized word overlap ratio provides a lexical signal that complements semantic similarity for cases where embedding representations diverge from human rater intuition.

**Layer 4 — Zero-score Rules (Score 0)**
Empty transcriptions, `[gibberish]`-tagged content, and responses of 1–2 words are deterministically assigned score 0.

### Pre-processing
EIT-specific transcription notation stripped before scoring:
- `[pause]`, `[gibberish]` and similar bracket tags removed
- `xxx` tokens removed
- Unicode NFD normalization applied for accent-invariant matching

### Threshold Calibration
Thresholds were calibrated empirically against the provided example transcriptions in the scoring sheet, then adjusted to better match the rubric criteria:
- Score 3: cosine similarity ≥ 0.92 OR word overlap ≥ 0.90
- Score 2: cosine similarity ≥ 0.75 OR word overlap ≥ 0.70
- Score 1: cosine similarity ≥ 0.45 OR word overlap ≥ 0.40
- Score 0: below all thresholds, or empty/gibberish

### Output
Sentence-level scores (0–4) written to column 4 of the standard AutoEIT Excel format across 4 participant sheets (~120 utterances total).

### Known Limitations
- Semantic similarity models may not perfectly capture all meaning distinctions relevant to L2 Spanish assessment
- Edge cases such as synonymous substitutions and grammar changes that do not affect meaning are difficult to handle automatically without a gold-standard calibration dataset
- Human validation against gold-standard scores would be required for production deployment

---

## Setup

```bash
python -m venv venv

# Windows
.\venv\Scripts\activate

# Mac/Linux
source venv/bin/activate

pip install -r requirements.txt
```

### ffmpeg (required for Whisper)
ffmpeg must be installed separately on your system and available on PATH. Whisper will fail without it.

```bash
# Windows: download from https://ffmpeg.org/download.html and add to PATH
# Mac
brew install ffmpeg

# Linux
sudo apt install ffmpeg
```

### Requirements
See `requirements.txt`. Key dependencies:
- `openai-whisper`
- `sentence-transformers`
- `openpyxl`
- `pandas`
- `torch`
- `ffmpeg` (system-level, see above)

---

## Applicant
**Kashish Shaikh**
- Email: kashishshaikh705@gmail.com
- GitHub: [github.com/kashishshaikh007](https://github.com/kashishshaikh007)
- LinkedIn: [linkedin.com/in/07kashish-shaikh](https://linkedin.com/in/07kashish-shaikh)
- Portfolio: [kashishshaikh007.github.io](https://kashishshaikh007.github.io)
