# MindCare AI — RIO Internship Project

A learning prototype for general wellbeing support. It combines React, FastAPI, Gemini, Hugging Face Transformers emotion classification, and SQLite.

## Requirements
- Python 3.10–3.12 recommended
- Node.js 20+ and npm
- Internet connection for first Hugging Face model download and Gemini API calls
- A Gemini API key from https://aistudio.google.com/app/apikey

## 1. Backend setup

Open a terminal:

```bash
cd mindcare-ai/backend
python -m venv .venv
```

Activate the environment:

Windows PowerShell:
```powershell
.venv\Scripts\Activate.ps1
```

Windows CMD:
```cmd
.venv\Scripts\activate.bat
```

macOS/Linux:
```bash
source .venv/bin/activate
```

Install packages:
```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Create `.env` by copying `.env.example`. Add your Gemini key:

Windows PowerShell:
```powershell
Copy-Item .env.example .env
```

macOS/Linux:
```bash
cp .env.example .env
```

Edit `.env` and set `GEMINI_API_KEY=your_real_key`. Never commit `.env` or expose the key in React.

Start backend:
```bash
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

Open http://127.0.0.1:8000/docs to inspect and test the API. Open http://127.0.0.1:8000/api/diagnostics to check whether the Gemini key is configured and which models are selected. The chat endpoint falls back to a lightweight emotion estimate if the Hugging Face model takes over 12 seconds on first download, and Gemini calls have explicit timeouts.

On the first chat request, Hugging Face downloads the emotion model (several hundred MB). The first prediction can take longer. The model is cached for later runs. If your computer has limited RAM or you want to test the API before downloading the model, set `HF_ENABLED=false` in `.env`, restart the backend, and use the keyword fallback. Set it back to true to use Transformers.

## 2. Frontend setup

Open a second terminal:

```bash
cd mindcare-ai/frontend
npm install
npm run dev
```

Open the URL Vite prints, usually http://localhost:5173.

Optional: create `frontend/.env` with:
```env
VITE_API_URL=http://localhost:8000
```
Restart Vite after changing environment variables.

## 3. Run both parts

Keep two terminals open:
- Terminal 1: backend activated, running `uvicorn app.main:app --reload --port 8000`
- Terminal 2: frontend running `npm run dev`

Visit http://localhost:5173.

## 4. Test

Backend tests:
```bash
cd backend
pytest -q
```

API health check:
```bash
curl http://127.0.0.1:8000/api/health
```

Chat request (Gemini key required for a generated answer; crisis test does not call Gemini):
```bash
curl -X POST http://127.0.0.1:8000/api/chat -H "Content-Type: application/json" -d '{"message":"I feel stressed about exams","session_id":"demo-1","history":[]}'
```

## Features
- AI chat with recent conversation context
- Hugging Face emotion classification (anger, disgust, fear, joy, neutral, sadness, surprise)
- Crisis phrase screening with a fixed supportive response pathway
- Mood check-ins stored in SQLite
- Journal entries stored in SQLite
- FastAPI interactive docs

## Important limitations and safety
- This is a student prototype, not a medical device, diagnostic tool, therapist, or emergency service.
- Emotion classification can be wrong, is English-oriented, and must not be treated as clinical evidence.
- The crisis phrase matcher is intentionally simple and is NOT a validated risk detector. It can miss indirect statements and can produce false positives. Do not deploy it for real users or rely on it for safety decisions.
- For any real pilot, obtain qualified clinical review, implement a professionally designed crisis protocol, verify country-specific resources, perform security/privacy review, add authentication and per-user authorization, encryption, retention/deletion controls, rate limiting, abuse protection, and informed consent.
- The starter saves chat messages, moods, and journals in a local SQLite file without authentication or encryption. Use fictional test data only.
- Do not submit real people's sensitive mental-health conversations to external APIs without informed consent and appropriate institutional/privacy approval.

## Project structure

```text
mindcare-ai/
  backend/
    app/
      ai.py
      config.py
      database.py
      emotion.py
      main.py
      models.py
      safety.py
      schemas.py
    tests/
    .env.example
    requirements.txt
  frontend/
    src/App.jsx
    src/main.jsx
    src/styles.css
    index.html
    package.json
  README.md
```
