# Plan: Job Search Agent (Claude Code)

> Status: research done, not built yet. Intended to move into its own repository once work starts.
> Research date: 2026-11-10.

## Goal

An agent that finds relevant jobs every day, scores how well each one fits, and drafts tailored applications.
**A human always makes the final decision and does the sending.**

## Architecture

```
Profile (CV, skills, preferences, deal-breakers)
        │
1. Find jobs ──► job APIs, company career pages, pasted links
2. Filter ────► remove duplicates; hard rules (location, salary, language, visa)
3. Score fit ─► Claude scores 1–5 against the profile, with reasons and gaps
4. Research ──► company, team, recent news, likely hiring manager
5. Draft ─────► tailored CV + cover letter, then a second pass critiques and revises
6. Review ────► human approves, edits or skips, then applies themselves
7. Track ─────► archive the posting, the documents sent and the outcome; follow-up reminders
```

## Data sources

| Source | Access | Notes |
|---|---|---|
| Bundesagentur für Arbeit (Jobsuche) | Free; base URL `https://rest.arbeitsagentur.de/jobboerse/jobsuche-service`, header `X-API-Key: jobboerse-jobsuche` | Largest German source. **Not an official API** (community docs: bundesAPI); can change without notice. Search returns reference numbers; details need a separate call using the Base64-encoded reference. |
| Arbeitnow | Free, no key | Germany/Europe, refreshed hourly, has a visa-sponsorship filter. |
| Greenhouse / Lever / Ashby job boards | Free, no key, one company per call | Use for a list of target companies, checked daily. No search across companies. |
| Adzuna, JSearch (RapidAPI) | Key required, free tier | Broad aggregators; check terms and pricing first. |
| LinkedIn / StepStone / Indeed | No permitted API | **Do not scrape.** Paste links or posting text in by hand for scoring and drafting. |

## Rules

1. **One file holds every true fact.** The agent may only use facts from the profile file: no invented numbers, job titles or skills.
2. **Hard filters before scoring.** Cheaper, and avoids "good fit, but 300 km away".
3. **A second pass critiques each draft** against the job posting.
4. **A human decides what gets sent.** No automated mass applications.
5. **Track everything:** posting, the CV and cover letter that were sent, status, outcome.
6. Keep GDPR in mind when storing data about contacts or hiring managers.

## Proposed structure

```
job-agent/
├── CLAUDE.md            # profile, preferences, rules for the agent
├── cv/master.md         # master CV (the only source of facts)
├── fetch_jobs.py        # BA Jobsuche + Arbeitnow + Greenhouse/Lever target companies
├── targets.yaml         # target companies with their job board type and slug
├── .claude/skills/
│   ├── score-job/       # fit score 1–5 with reasons and gaps
│   └── draft-application/  # CV + cover letter, draft → critique → revise
└── applications/        # one folder per application: posting, documents, status
```

Running: a scheduled Claude Code cloud session each morning fetches new jobs, scores them, and drafts applications
for the top 3–5. The human reviews them.

## Open questions before building

- Field, location / remote, experience level
- Applications in German, English or both
- Output format: Markdown/PDF or LaTeX
- Where tracking lives: a Markdown table, Google Sheet or Notion

## Projects to learn from

- ai-job-search (MadsLorentzen): Claude Code, `/apply <url>`, profile in CLAUDE.md, LaTeX PDFs
- Career-Ops (santifer): scoring on 10 dimensions, multi-agent setup
- Job Hunter AI / Career OS: Python + Claude API, company research before drafting

## Sources

- https://github.com/bundesAPI/jobsuche-api
- https://www.arbeitnow.com/blog/job-board-api
- https://jobspipe.dev/blog/free-job-api
- https://jobspipe.dev/blog/public-vs-gated-ats-apis
- https://dev.to/chaazee/comparing-the-job-posting-apis-of-greenhouse-lever-ashby-smartrecruiters-and-recruitee-2026-4b60
- https://cdn.jsdelivr.net/gh/MadsLorentzen/ai-job-search@master/README.md
- https://dev.to/santifer/i-built-a-multi-agent-job-search-system-with-claude-code-631-evaluations-12-modes-2cd0
- https://dev.to/akram_bakhouche/career-os-architecture-how-i-built-a-job-search-agent-with-claude-sdk-24g5
