# AutoEIT - GSoC 2026 Evaluation Test

## Project
Automated Elicited Imitation Test (AutoEIT) for HumanAI Foundation GSoC 2026.

## Tests Completed
- **Test I**: Audio transcription of Spanish EIT recordings using OpenAI Whisper
- **Test II**: Automated scoring of transcriptions using meaning-based rubric

## Approach

### Test I - Transcription
- Used OpenAI Whisper (base model) for ASR
- Language set to Spanish explicitly
- Transcriptions reflect exact learner production including disfluencies
- ASR errors corrected, but learner grammar/vocabulary errors preserved
- Known limitation: Whisper may auto-correct some learner errors

### Test II - Scoring
- Implemented meaning-based scoring rubric (Ortega, 2000) with scores 0-4
- Used multilingual sentence embeddings for semantic similarity
- Special notation ([pause], [gibberish], xxx) stripped before scoring
- Thresholds calibrated against provided example transcriptions

## Setup
```powershell
python -m venv venv
.\venv\Scripts\activate
pip install -r requirements.txt
```

## Requirements
See `requirements.txt`