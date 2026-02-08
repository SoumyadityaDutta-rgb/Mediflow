Purpose
-------
This file gives concise, actionable guidance to AI coding agents working in this repository so they can be productive immediately.

Quick architecture summary
--------------------------
- backend/: FastAPI-based API + LangChain agent glue. Key files:
  - `backend/aiagent.py` — builds the LangChain agent, tools list and `SYSTEM_PROMPT` used by the agent.
  - `backend/tools.py` — low-level integrations (Ollama chat wrapper `query_medgemma`, Twilio `call_emergency_contact`). These functions return plain strings or raise `RuntimeError` on failure.
  - `backend/config.py` — credentials and constants (TWILIO_*, GROQ_API_KEY, EMERGENCY_CONTACT_NUMBER). Treat these as secrets; currently some values are placeholders / hard-coded and should be moved to environment variables.
  - `backend/main.py` — FastAPI app and an entrypoint that programmatically runs Uvicorn. Note: the file currently calls `uvicorn.run("main:app",...)` which expects an importable `main` module; prefer running as a module (`python -m backend.main`) or adjust to `backend.main:app` when using `uvicorn` cli.
- frontend: `frontend.py` — Streamlit UI that POSTs JSON to `http://localhost:8000/ask` (backend `/ask` endpoint expects JSON {"message": "..."}).
- top-level `main.py` currently empty — ignore for runtime purposes; backend's module is `backend/main.py`.

Key patterns and conventions (do not change without reason)
-------------------------------------------------------
- LangChain agent: `backend/aiagent.py` creates Tool objects with `Tool.from_function(...)`. Tools are simple sync functions that accept strings and return strings (or raise an exception). Example tool names: `ask_mental_health_specialist`, `call_emergency`, `find_nearby_therapists_by_location`.
- Error signalling: integration failures usually raise `RuntimeError` (see `query_medgemma` in `backend/tools.py`). Upstream wrappers catch these and provide safe user-facing text.
- System prompt: `aiagent.SYSTEM_PROMPT` contains the conversational safety and role instructions. When changing behavior, prefer editing this prompt rather than changing agent wiring.
- Response parsing: `aiagent.parse_response(stream)` expects the agent/stream items to be dicts with `tools` and `agent` keys and extracts `tool` names and final assistant content.

Integration points & external dependencies
-----------------------------------------
- Ollama: `backend/tools.py` calls `ollama.chat(model='alibayram/medgemma:4b', ...)`. The repo assumes an Ollama instance and the MedGemma model present locally or accessible.
- TWILIO: `backend/tools.py` uses `twilio.rest.Client` to place calls (`call_emergency_contact`). Provide TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN, TWILIO_FROM_NUMBER via environment variables and update `backend/config.py` (do NOT commit real secrets).
- GROQ ChatGroq: `aiagent.ChatGroq` is instantiated with `GROQ_API_KEY` from `backend/config.py`.
- LangChain + langchain_groq are used; agent type: `AgentType.CHAT_ZERO_SHOT_REACT_DESCRIPTION`.

Developer run / debug commands (discovered in repo)
-------------------------------------------------
- Start the backend (preferred):
  - Run as module so `uvicorn` resolves the correct module path:
    ```powershell
    python -m backend.main
    ```
  - Or start uvicorn directly (recommended change to avoid `main:app` mismatch):
    ```powershell
    uvicorn backend.main:app --reload --host 0.0.0.0 --port 8000
    ```
- Start the frontend Streamlit UI:
  ```powershell
  streamlit run frontend.py
  ```
- Make sure Ollama is running and the model is available (example used in repo):
  - Ollama run or pull the model used in `tools.py`: `alibayram/medgemma:4b`.

Files to inspect when modifying behavior
----------------------------------------
- `backend/aiagent.py` — agent wiring, tools list, system prompt, parse_response.
- `backend/tools.py` — external API wrappers; keep string return contract intact.
- `backend/config.py` — secrets and toggles. Replace hard-coded keys with environment readouts.
- `frontend.py` — UI and network contract for POST /ask; editing here affects how messages are formatted when sent to backend.

Small, concrete examples
------------------------
- To add a new tool:
  1. Add a small function in `backend/tools.py` that accepts a string and returns a string (raise `RuntimeError` on failure).
  2. Add a `Tool.from_function(...)` entry in `backend/aiagent.py` and include a clear `description` used by the agent.
  3. Add tests or a quick manual validation by calling the function and/or calling POST /ask with an input that should invoke it.

Notes & safety
---------------
- Secrets: `backend/config.py` currently contains placeholders and at least one hard-coded API key — rotate and move to environment variables immediately.
- Safety: this project wires a mental-health–focused assistant with an emergency call action. Avoid changing `call_emergency_contact` behavior without coordination — it will initiate real calls if Twilio credentials are configured.

If anything above is unclear or you need examples expanded (e.g., exact lines to change to switch uvicorn import), tell me which area and I will update this file.
