# AI Resume Tailor

Paste a job description, get a tailored ATS-optimized resume (and optional cover letter) built from your preloaded profile — with match scoring and application history.

## Features

- **JD parsing** — extracts company, role, skills, and keywords via OpenAI structured JSON
- **Token-efficient ranking** — local TF-IDF + keyword overlap picks only the most relevant experience/projects (no LLM cost)
- **Tailored resume** — rewrites bullets and summary to align with the JD
- **ATS scoring** — local keyword match + LLM gap summary; auto-retry if score &lt; 70%
- **PDF export** — HTML templates rendered to PDF via Playwright
- **Cover letter** — optional generation from the same JD context
- **History** — tracks every application with company, role, ATS %, and download links

## Prerequisites

- Python 3.11+
- Node.js 18+
- OpenAI API key

## Setup

### 1. Backend

```bash
cd backend
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS/Linux
source .venv/bin/activate

pip install -r requirements.txt
playwright install chromium

copy .env.example .env   # Windows
# cp .env.example .env   # macOS/Linux
```

Edit `.env` and set your `OPENAI_API_KEY`.

Edit `data/profile.json` with your real details, experience, projects, and skills.

Start the API:

```bash
uvicorn app.main:app --reload --port 8000
```

### 2. Frontend

```bash
cd frontend
npm install
npm run dev
```

Open [http://localhost:5173](http://localhost:5173)

## Usage

1. **Profile tab** — review/edit your JSON profile (saved to `backend/data/profile.json`)
2. **Generate tab** — paste a job description, optionally check cover letter + auto-download, click Generate
3. **History tab** — see all past applications with company, role, ATS score, and PDF downloads

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/generate` | Generate tailored resume (+ optional cover letter) |
| GET | `/api/profile` | Get profile JSON |
| PUT | `/api/profile` | Update profile JSON |
| GET | `/api/history` | List all applications |
| GET | `/api/download/{id}/{type}` | Download resume or cover_letter PDF |
| GET | `/api/health` | Health check |

## Project Structure

```
backend/
  app/
    services/     # LLM, ranker, scorer, renderer
    routes/       # API endpoints
    templates/    # HTML resume & cover letter
  data/
    profile.json  # Your preloaded data
frontend/
  src/
    components/   # Generate, Profile, History, ScorePanel tabs
```

## Cost Notes

Typical cost per generation with `gpt-4o-mini` is ~$0.001–0.003 depending on JD length and whether cover letter is included. JD hash caching avoids re-parsing duplicate postings.
