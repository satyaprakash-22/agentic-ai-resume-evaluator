# AI Resume Evaluator

An agentic AI workflow that parses resumes and evaluates candidate profiles against job descriptions using n8n and Groq LLM.

## Features
- LLM-driven skill extraction from resume text
- Relevance scoring (0-100) against job description
- Gap analysis identifying missing requirements
- Automated hiring recommendation (Hire / Maybe / Reject)

## Tech Stack
- **n8n** — workflow orchestration and automation
- **Groq AI (Llama 3)** — LLM for skill extraction and scoring
- **HTML/CSS/JS** — frontend interface

## How to Run Locally

### Prerequisites
- Docker Desktop installed

### Steps
1. Clone this repo
2. Run n8n:
docker-compose up -d
3. Open http://localhost:5678
4. Import `workflow.json` into n8n (click + → Import from file)
5. Add your Groq API key to the Groq credentials in n8n
6. Publish the workflow
7. Open `index.html` in your browser

## Architecture
Webhook → Skill Extraction (LLM) → Relevance Scoring + Gap Analysis (LLM) → JSON Response