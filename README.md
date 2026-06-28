# gov-bot

A research assistant that answers questions about U.S. government data by drawing
on two federal APIs — **Congress.gov** (legislation, members, votes, committees,
nominations, treaties, the Congressional Record) and **OpenFEC** (candidates,
committees/PACs, and campaign finance: receipts, disbursements, independent
expenditures, filings). It can mix both sources in a single answer — e.g. a
lawmaker's bills alongside their campaign fundraising.

<img width="1370" height="767" alt="Screenshot 2026-06-28 at 11 21 34 AM" src="https://github.com/user-attachments/assets/8ad7f6ef-615a-41ed-817b-8a45987b6255" />

## Orchestration

A three-layer multi-agent setup. Capabilities are organized as **skills**,
namespaced `<source>/<group>` (e.g. `congress/bill`, `fec/candidate`), each backed
by a `SKILL.md` endpoint reference.

1. **Orchestrator** (root) — lists the available skills, picks the ones relevant
   to the question (from either source), and fans out one researcher per skill,
   then synthesizes a final answer.
2. **Skill researcher** (one per selected skill) — gets a fetch plan, performs the
   API calls, and returns structured findings.
3. **Fetch planner** — reads a skill's endpoint reference and proposes the most
   useful API fetches for the sub-question.

A cross-cutting **document analyst** downloads and extracts text from government
documents (bill text, reports, hearings — PDF/HTML/text, with OCR for scanned
PDFs), available to both the researchers and the orchestrator.

## Tech stack

- **Google ADK** (Agent Development Kit) for agent orchestration and tooling
- **LiteLLM** running **Claude (Opus 4.8)** as the model for every agent
- **requests** for the Congress.gov / OpenFEC REST calls
- **pypdf** + optional **pdf2image / pytesseract / Pillow** (with `poppler` and
  `tesseract`) for document extraction and OCR

