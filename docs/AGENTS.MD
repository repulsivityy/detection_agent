# Agents: Detection Agent

## 1. Detection Agent
**Role:** Automates the feedback loop between threat intelligence (Project Harimau) and SIEM (Google SecOps) using Gemini 3 and the Google GenAI SDK.

### Inputs
- **Harimau Insights**: Forward indicators via webhook push.
- **SIEM Data**: Alerts, cases, and logs via oneMCP.
- **Analyst Chat**: Ad-hoc questions via the chat frontend.

### Outputs
- **Case Comments**: Markdown reports written natively into Google SecOps cases.
- **SIEM Search Results**: IOC lookups via SIEM search for indicators not yet tied to a case.
- **Tag Management**: Utilizing `harimau-investigating` and `harimau-completed` tags for state tracking.
- **Harimau Triggers**: Submitting deep-dive investigation tasks to Project Harimau.

### Lifecycle

**Workflow A - Harimau Triggered:**
1. **Triggered** by Harimau webhook push (authenticated via Pre-shared API Key).
2. **Searches** SecOps for existing cases containing the same IOCs.
3. **Runs** a SIEM search for the IOCs to find broader telemetry hits.
4. **Synthesizes** findings and writes a Markdown report as an Analyst Comment on any matching SecOps case(s).

**Workflow B - Schedule Triggered:**
1. **Triggered** hourly via a scheduler.
2. **Fetches** the most critical alert from Google SecOps (primarily by severity) that is NOT tagged `harimau-investigating` or `harimau-completed`.
3. **Tags** the alert with `harimau-investigating`.
4. **Sends** the IOC to Project Harimau to kick off an investigation.
5. **Receives** a callback from Harimau upon completion and updates the tag to `harimau-completed`.

**Workflow C - Analyst Interactive (Phase 2):**
1. **Triggered** by an analyst via the Web Frontend.
2. **Accepts** natural language queries regarding SecOps data.
3. **Executes** oneMCP lookups and returns conversational insights directly to the analyst.
4. **Conversation history** is held in-memory per session (durable persistence via Cloud SQL is a future enhancement).

### Error Handling
- If the oneMCP server is unreachable, return a clear error: "oneMCP server is unavailable." No retry logic — oneMCP is a Google-managed SaaS and there is no alternative access path to SecOps.