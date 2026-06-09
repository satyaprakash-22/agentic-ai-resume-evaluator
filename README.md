# Agentic AI Resume Evaluator

An agentic AI workflow that automatically parses resumes and evaluates candidate profiles against job descriptions — returning a relevance score, matched skills, skill gaps, and a hiring recommendation.

Built with **n8n** for workflow orchestration and **Groq LLM** for AI reasoning, with a clean frontend deployed on Vercel.

---

## Live Demo

Frontend: [https://agentic-ai-resume-evaluator.vercel.app/]

> The n8n workflow runs locally via Docker. To use the live demo, follow the setup steps below to start n8n on your machine, then open the frontend URL.

---

## What It Does

1. User uploads a resume (PDF, DOCX, DOC, or TXT) or pastes resume text
2. User pastes a job description
3. The frontend sends both to an n8n webhook
4. n8n orchestrates two chained LLM calls:
   - **Chain 1 — Skill Extraction:** Extracts technical skills, soft skills, experience, education, and job titles from the resume
   - **Chain 2 — Evaluation:** Scores candidate relevance (0–100), identifies matched skills, performs gap analysis, and gives a hiring recommendation (Hire / Maybe / Reject)
5. Results are returned as structured JSON and displayed in the UI

This is an **agentic pipeline** — each LLM call's output feeds as context into the next, enabling multi-step autonomous reasoning without human intervention at each step.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Workflow Orchestration | n8n (self-hosted via Docker) |
| LLM | Groq API — llama-3.3-70b-versatile |
| Frontend | HTML, CSS, Vanilla JavaScript |
| PDF Parsing | PDF.js (client-side) |
| DOCX Parsing | Mammoth.js (client-side) |
| Frontend Deployment | Vercel |
| Backend Containerization | Docker |

---

## Project Structure

```
agentic-ai-resume-evaluator/
├── index.html           # Frontend UI (file upload, results display)
├── docker-compose.yml   # Docker config for running n8n locally
├── workflow.json        # Exported n8n workflow (import this into n8n)
└── README.md
```

---

## How to Run Locally

### Prerequisites
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- A free [Groq API key](https://console.groq.com) (no credit card required)

### Step 1 — Start n8n

Open a terminal and run:

```bash
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Keep this terminal open — closing it stops n8n. Once you see `Editor is now accessible via: http://localhost:5678`, open that URL in your browser.

### Step 2 — Import the Workflow

1. In n8n, click the **+** icon to create a new workflow
2. Click the **⋯** menu (top right) → **Import from file**
3. Select `workflow.json` from this repo
4. The full workflow will load with all nodes configured

### Step 3 — Add Your Groq API Key

1. Click on either **Groq Chat Model** node in the canvas
2. Under **Credentials**, click **Create new credential**
3. Paste your Groq API key and save
4. Repeat for the second Groq Chat Model node (select the same credential)

### Step 4 — Publish the Workflow

Click the **Publish** button (top right of the canvas) to activate the workflow. The webhook endpoint is now live at:

```
http://localhost:5678/webhook/resume-evaluator
```

### Step 5 — Open the Frontend

Simply open `index.html` in your browser (double-click the file). Upload a resume and paste a job description to run an evaluation.

---

## How the n8n Workflow Works

```
Webhook (POST)
     │
     ▼
Basic LLM Chain — Skill Extraction
     │  Prompt: Extract technical skills, soft skills,
     │  experience, education from resume text.
     │  Output: Structured JSON of candidate profile
     ▼
Basic LLM Chain — Evaluation
     │  Prompt: Given candidate skills + job description,
     │  return relevance score, matched skills,
     │  gap analysis, and hiring recommendation.
     │  Output: Structured JSON evaluation
     ▼
Respond to Webhook
     │  Returns final JSON to frontend
```

Both LLM chains use **Groq's llama-3.1-8b-instant** model and are prompted to return strict JSON with no markdown or extra text.

---

## Supported Resume Formats

| Format | How it works |
|---|---|
| PDF | Parsed client-side using PDF.js — works with text-based PDFs |
| DOCX / DOC | Parsed client-side using Mammoth.js |
| TXT | Read directly as plain text |
| Manual paste | Always available as fallback |

> Note: Scanned/image-based PDFs cannot be parsed since they contain no machine-readable text. Use the manual paste option for those.

---

## Sample Output

```json
{
  "RELEVANCE_SCORE": 88,
  "MATCHING_SKILLS": ["Python", "React", "Node.js", "WebSockets", "PostgreSQL"],
  "GAP_ANALYSIS": ["AWS experience", "System design exposure"],
  "RECOMMENDATION": "Hire",
  "RECOMMENDATION_REASON": "Candidate has strong project experience directly aligned
  with the role requirements, including real-time data systems and full-stack development."
}
```

---

## Deployment Notes

**Frontend** is deployed on [Vercel](https://vercel.com) — any push to the `main` branch triggers an automatic redeployment.

**Backend (n8n)** runs locally via Docker for development. For a production setup:
- Deploy n8n on [Railway](https://railway.app) using the n8n template
- Update the `WEBHOOK_URL` in `index.html` to the Railway public URL
- Add webhook authentication and rate limiting to protect the API key from abuse
- Store the Groq API key as an environment variable, not hardcoded

---

## Author

**Satya Prakash Vempati**
B.Tech Information Technology - VNRVJIET, Hyderabad
