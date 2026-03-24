# Product Requirements Document: Detection Agent

## 1. Objective
Automate the feedback loop between Google SecOps SIEM (lagging indicators) and Project Harimau (forward threat indicators).

## 2. Background
As of Phase 7, the Detection Agent was decoupled from the Project Harimau repository. The goal is to create a bidirectional feedback loop.

## 3. Core Features

### 3.1 Inbound from Harimau (Workflow A)
- Receive forward threat indicators via `HTTP-JSON-RPC` webhook from Harimau's Lead Hunter.
- Search SecOps for existing cases containing the same IOCs.
- Run a SIEM search for broader telemetry hits.
- Write a Markdown synthesis report as an Analyst Comment on matching SecOps case(s).

### 3.2 Scheduled Threat Hunting (Workflow B)
- Run on a schedule (e.g., hourly) to fetch the most critical alert (primarily by severity).
- Exclude alerts already tagged `harimau-investigating` or `harimau-completed`.
- Tag the selected alert as `harimau-investigating` before triggering Harimau.
- Receive a callback from Harimau upon investigation completion and update the tag to `harimau-completed`.

### 3.3 Interactive Analyst Chat (Workflow C)
- Provide a web frontend for analysts to chat with the Detection Agent.
- Support ad-hoc SecOps queries via natural language using Gemini 3.
- Conversation history held in-memory per session (future: persist to Cloud SQL).

### 3.4 Agent Execution Model
- Stateless execution using Gemini 3 via the Google GenAI SDK.
- No LangGraph or persistent state graph required.

## 4. Non-Functional Requirements
- Webhook endpoint secured via Pre-shared API Key.
- If oneMCP is unreachable, return a clear error — no retry logic (Google-managed SaaS, no alternative access path).
- Must be independently deployable and scalable.
