# 🌟 AI Music Generation and MIDI Playback Web App

This project combines deep learning, music processing, and language models to build a full-featured AI music generation system. It lets you:

- 🎵 Train a Conv1D neural network on classical MIDI files.
- 🎶 Generate original music sequences from trained models or using a language model (Groq LLM).
- 🌐 Upload and play MIDI files in-browser with an interactive visualizer using Magenta.js.

---

## 📚 Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [1. Model Training - `train.ipynb`](#1-model-training---trainipynb)
- [2. Web App - `app.py` + `index.html`](#2-web-app---apppy--indexhtml)
- [3. LangChain + Groq LLM Integration](#3-langchain--groq-llm-integration)
- [4. Advanced Composition: Happy Birthday Generator](#4-advanced-composition-happy-birthday-generator)
- [Suggestions & Enhancements](#suggestions--enhancements)

---

## ✨ Features

- Train a neural network on classical piano MIDI data.
- Predict next notes using either greedy or probabilistic sampling.
- Convert predicted notes to playable `.mid` files.
- Integrate an LLM (via Groq API) to generate MIDI note sequences.
- Upload your own MIDI and play with visualizer controls (piano-roll, staff, waterfall).

---

## ⚙️ Installation

```bash
# Clone repo and navigate to project
pip install -r requirements.txt

# Run Flask web app
python app.py
```

Also install:
```bash
pip install tensorflow==2.12.0 keras==2.12.0 music21 langchain langchain_groq
```

---

## 📁 Project Structure

```
project/
│
├── train.ipynb         # Neural network training & generation logic
├── app.py              # Flask web server
├── templates/
│   └── index.html      # Upload + playback UI
└── static/uploads/     # Stores uploaded MIDI files
```

---

## 1. 🚀 Model Training - `train.ipynb`

### Step-by-step:

#### 📝 Reading MIDI Files
- Uses `music21` to parse `.mid` files from `/content/schubert`.
- Extracts only piano notes and chords, stored as string labels.

#### 🔢 Data Preprocessing
- Flattens all notes into a single list.
- Filters out notes with frequency < 50.
- Creates input sequences (32 notes) and corresponding next-note output.
- Maps notes to integers for training.

#### 💪 Model Architecture

```python
Embedding -> Conv1D -> Dropout -> MaxPool1D
         -> Conv1D (dilated) -> Dropout -> MaxPool1D
         -> Conv1D (dilated) -> Dropout -> MaxPool1D
         -> GlobalMaxPool1D -> Dense -> Softmax
```

- Learns temporal features in note sequences using **causal Conv1D layers**.
- **Dilation** helps capture long-term dependencies.
- Output is a probability over possible next notes.

#### 🎓 Training
- Loss: `sparse_categorical_crossentropy`
- Optimizer: `Adam`
- Uses `ModelCheckpoint` to save best model.

#### 🎶 Prediction + MIDI Conversion
- Multiple generation styles:
  - Argmax (most probable note)
  - Probabilistic sampling from softmax
  - Seeded from a melody (e.g., "Happy Birthday")

- Predictions are mapped back to note strings and converted into `.mid` files using `music21.stream.Stream()`.

---

## 2. 🌐 Web App - `app.py` + `index.html`

### Flask Backend (`app.py`)
- Accepts file uploads at `/` endpoint.
- Saves valid `.mid/.midi` files to `static/uploads/`

### HTML + MIDI Player (`index.html`)
- Uses:
  - [Magenta.js](https://magenta.tensorflow.org/js)
  - [html-midi-player](https://github.com/cifkao/html-midi-player)
- Features:
  - Upload interface
  - Visualizer type selection (Piano Roll, Staff, Waterfall)
  - Autoplay, loop, volume control

---

## 3. 🔎 LangChain + Groq LLM Integration

- Calls a Groq-powered LLM (e.g., LLaMA 3) to generate note integers:

```python
"Generate a list of 32 MIDI notes between 21 and 108"
```

- Parses the output and converts it to a MIDI sequence.
- Adds filtering to skip invalid values.
- Saves final music as `music.mid`

---

## 4. 🎶 Advanced Composition: Happy Birthday Generator

- Predefined note + chord structure for the full "Happy Birthday" melody.
- Uses `note.Note` and `chord.Chord` objects.
- Appends notes and chords to a `stream.Stream`, exported to MIDI.

---

## 🚀 Suggestions & Enhancements

### 🔄 Refactoring
- Move training logic to `train_model.py`
- Separate prediction into `predict.py`

### 💾 Download Button for Generated Files
- Allow user to download generated `.mid`

### 🔐 Security
- Use `.env` to hide API keys
- Validate Groq output with stricter parsers

### 🌍 Host on Cloud
- Deploy Flask app on Render, Railway, or Hugging Face Spaces.

### 🚀 Add Genre Conditioning
- Add genre/style inputs (e.g., jazz, lo-fi, EDM)
- Train with tags or use LLM for style-aware generation

---

## 📖 References
- [Magenta.js](https://magenta.tensorflow.org/js)
- [music21](https://web.mit.edu/music21/)
- [LangChain](https://www.langchain.com/)
- [html-midi-player](https://github.com/cifkao/html-midi-player)
- [Groq LLMs](https://console.groq.com)

---

## ❤️ Built With
- **Python** (Flask, music21, Keras)
- **JavaScript** (Magenta.js, Tone.js, MIDI Player)
- **LLM Integration** (LangChain + Groq)
- **Design**: Modern, dark-themed UI for music lovers

Enjoy generating AI music! 🎵

