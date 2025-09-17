# Multimodal

Lightweight Python toolkit for end-to-end speech and text workflows:

- speech-to-text transcription (VOSK)
- speech NER anonymization (beep-out entities)
- speech sentiment analysis
- speech question answering (extractive QA)
- text generation from context
- document to speech (PDF/DOCX → TTS)
- YouTube audio ingestion and local files

All required models are auto-downloaded on first use to a cache folder in `~/multimodal/resources`.

## Installation

```bash
pip install -r requirements.txt
pip install .
```

Python 3.8+ is recommended. Windows is supported; Linux/macOS should work as well (FFmpeg required or auto-handled on Windows).

## Quickstart

```python
from multimodal import MultiModal

# 1) Anonymize named entities in speech (insert beep sound over detected spans)
sa = MultiModal("speech_ner_anonymizer")
sa.load("https://www.youtube.com/watch?v=NKWKDyDKGzw", save_folder="test_files")
sa.anonymize()
sa.export()  # writes *_modified.wav next to the input audio

# 2) Sentiment analysis over transcribed speech
ss = MultiModal("speech_sentiment")
ss.load("test_files/Leonardo DiCaprios Powerful Climate Summit Speech.wav")
ss.get_sentiment()

# 3) Question answering from speech context
sqa = MultiModal("speech_question_answering")
sqa.load("test_files/Leonardo DiCaprios Powerful Climate Summit Speech.wav")
sqa.get_answer("Who is Samuel?")

# 4) Document → speech (PDF or DOCX)
d2s = MultiModal("doc_to_audio")
d2s.load("test_files/1907.11932.pdf")
# d2s.load("test_files/Sample Text.docx")
d2s.speak()

# 5) Generate text continuations from recent context (from speech or docs)
sg = MultiModal("speech_generation")
sg.load("test_files/Leonardo DiCaprios Powerful Climate Summit Speech.wav")
sg.listen()                 # transcribe
sg.generate(n_sentences=2)  # generate continuations
sg.speak(generated=True)    # speak generated text
```

## Supported tasks

- `speech_ner_anonymizer`: Transcribe speech and mask PII/NER spans with beep inserts
- `speech_sentiment`: Sentence-wise sentiment over transcribed speech
- `speech_question_answering`: Extractive QA from transcribed speech
- `doc_to_audio`: Speak the contents of PDF/DOCX via TTS
- `speech_generation`: Generate text continuations from recent transcript/doc context

## Models and resources

On first run, models are downloaded to `~/multimodal/resources`:

- Speech-to-text: VOSK English model (`vosk-model-en-us-0.22`)
- Transformers (Hugging Face):
  - NER: `elastic/distilbert-base-uncased-finetuned-conll03-english`
  - Sentiment: `nlptown/bert-base-multilingual-uncased-sentiment`
  - QA: `mvonwyl/distilbert-base-uncased-finetuned-squad2`
  - Text generation: `gpt2`

YouTube audio is fetched via `youtube_dl`. PDFs are parsed with `pdfminer.six`, DOCX via `python-docx`. TTS is provided by `pyttsx3`.

## Platform notes

- Windows: The toolkit auto-downloads an FFmpeg build to `~/multimodal/resources` when needed for format conversion.
- Linux/macOS: Ensure `ffmpeg` is installed and on `PATH` (e.g., `sudo apt install ffmpeg` on Debian/Ubuntu).

## API at a glance

```python
mm = MultiModal(task_name)                 # construct with one of the supported tasks
mm.load(path_or_youtube_url, save_folder)  # load input (audio/PDF/DOCX)
mm.listen()                                # transcribe speech (where applicable)
mm.anonymize()                             # for speech_ner_anonymizer
mm.get_sentiment()                         # for speech_sentiment
mm.get_answer(question)                    # for speech_question_answering
mm.generate(n_sentences=1)                 # for speech_generation
mm.speak(generated=False)                  # speak input and/or generated text
mm.export(path=None)                       # export modified audio (e.g., anonymized)
```

## Examples

- See `demo_script.py` and `demo_notebook.ipynb` for runnable examples.
- Try the audio sample(s) in `test_files/` or point to a YouTube URL.

## License

Apache-2.0 (see `LICENSE`).
