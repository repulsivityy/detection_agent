# Implementation Plan: Detection Agent

## Phase 1: Basic Agent & Google SecOps Integration
- [ ] Initialize repository structure (README, PRD, architecture docs).
- [ ] Setup the base detection agent using Gemini 3 and the Google GenAI SDK.
- [ ] Integrate the agent with the Google SecOps oneMCP server.
- [ ] Implement SecOps capabilities: case search by IOC, SIEM search, read/write tags, post analyst comments.
- [ ] Create an hourly scheduled workflow (Workflow B):
  - [ ] Fetch most critical alert (by severity), excluding `harimau-investigating` / `harimau-completed` tags.
  - [ ] Tag the alert as `harimau-investigating`.
  - [ ] Format and send IOC to Project Harimau.
- [ ] Implement error handling: return clear error if oneMCP is unreachable.

## Phase 2: Interactive Analyst Frontend
- [ ] Setup a Streamlit (or equivalent) chat frontend.
- [ ] Connect the frontend to the base agent for ad-hoc SecOps queries (Workflow C).
- [ ] Conversation history held in-memory per session (MVP).
- [ ] Add durable chat persistence: store conversation history in Cloud SQL (`chat_sessions` table with `session_id`, `role`, `content`, `timestamp`) so analysts can resume conversations across sessions.

## Phase 3: A2A Communication from Project Harimau
- [ ] Setup FastAPI web server for webhook ingestion.
- [ ] Define the A2A `HTTP-JSON-RPC` webhook schema (Workflow A).
- [ ] Implement Pre-shared API Key authentication.
- [ ] On receiving Harimau indicators: search SecOps cases + run SIEM search, then post Markdown report as Analyst Comment.
- [ ] Implement callback endpoint for Harimau to notify completion → update tag from `harimau-investigating` to `harimau-completed`.

## Phase 4: Feedback Loop Automation & Deployment
- [ ] Containerize and document deployment processes.
- [ ] Create E2E tests simulating the Harimau webhook push and the hourly scheduled job.
