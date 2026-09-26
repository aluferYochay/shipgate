# ShipGate

**Paste English AI-agent acceptance criteria → Gemini generates eval checks → live HTTP run against an agent → shareable pass/fail scorecard.**

Solo MVP · Adaptive6 · real LLM + real agent calls (not simulated).

## Try it

- **GitHub Pages (UI):** https://aluferyochay.github.io/shipgate/
- **Worker (API + UI once deployed):** `https://shipgate.<your-subdomain>.workers.dev`  
  See `PARENT_NEXT_STEPS.md` if the badge says API offline / needs key.

Flow:

1. Paste acceptance criteria (optional: custom agent HTTPS URL)
2. **Generate eval suite** — `POST /api/generate` via Gemini (`gemini-3.8-flash`)
3. **Run against agent** — Worker POSTs each case to the agent (or built-in `/api/demo-agent`)
4. Copy the **shareable scorecard** link (`#r=` hash; no DB)

## Agent contract

Worker sends:

```json
POST {agentUrl}
Content-Type: application/json

{ "message": "<case input>", "input": "<same>", "text": "<same>" }
```

Reads reply from JSON `reply` | `response` | `message` | `output` | `text` | `content`, or raw text.

## API

| Route | Body | Response |
|-------|------|----------|
| `GET /api/health` | — | `{ ok, hasGeminiKey, model }` |
| `POST /api/generate` | `{ criteria, agentUrl? }` | `{ cases: [{id,name,input,expect}], model, mode }` |
| `POST /api/run` | `{ agentUrl, cases }` | `{ results: [{id,pass,detail,status,reply,judge}], judge, live }` |
| `POST /api/demo-agent` | `{ message }` | `{ reply }` — refund bot with intentional bugs |

Secret: Cloudflare Worker secret **`GEMINI_API_KEY`** (Google AI Studio).

## Local

```bash
cd worker
npm install
# optional for local LLM:
# echo 'GEMINI_API_KEY=...' > .dev.vars
npx wrangler dev
# open http://127.0.0.1:8787/
```

## Deploy

Documented in **`PARENT_NEXT_STEPS.md`** (Cloudflare login + `wrangler secret put GEMINI_API_KEY` + `wrangler deploy`).

## Honest labels

- Scorecards from a live run are marked **live HTTP** + judge mode (`gemini` or `heuristic`).
- Shared `#r=` links are frozen snapshots.
- Demo agent is labeled as hosted demo and includes known policy bugs on purpose.
