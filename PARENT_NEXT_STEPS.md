# Parent next steps — ShipGate backend deploy

## Status (executor verified locally, Asia/Jerusalem)

| Check | Result |
|-------|--------|
| `POST /api/generate` via Gemini `gemini-3.8-flash` | OK (10 real cases from refund criteria) |
| `POST /api/run` → live HTTP `/api/demo-agent` + Gemini judge | OK (e.g. 6/10 — demo bugs correctly fail) |
| `GET /api/health` `hasGeminiKey` | true when secret present |
| Cloudflare Worker **public** deploy | **Blocked** — no `wrangler login` / CF token on box |
| GitHub Pages UI | Push updates `https://aluferyochay.github.io/shipgate/` (API badge offline until Worker URL wired) |

Model note: `gemini-2.0-flash` is retired on this key; code uses **`gemini-3.8-flash`** (API recommendation).

## What you must do (≈5 minutes)

### A. Cloudflare account + token

1. [Cloudflare dashboard](https://dash.cloudflare.com/) → My Profile → API Tokens  
2. Create token with **Edit Cloudflare Workers** template  
3. Copy **Account ID** (Workers overview right sidebar)

### B. Deploy from your machine (fastest)

```bash
git clone https://github.com/aluferYochay/shipgate.git && cd shipgate/worker
npm install
npx wrangler login
npx wrangler deploy
# paste Gemini key when prompted (same value as box card.GEMINI_API_KEY / AI Studio):
npx wrangler secret put GEMINI_API_KEY
curl -s https://shipgate.<YOUR_SUBDOMAIN>.workers.dev/api/health
# expect hasGeminiKey: true
```

Open the Worker URL printed by wrangler — **that is the public try link** (serves UI + API).

### C. Or deploy via GitHub Actions

Copy `docs/deploy-worker.yml.example` → `.github/workflows/deploy-worker.yml` (needs a PAT/`gh` token with `workflow` scope; the executor OAuth token could not push workflows).


Repo secrets (Settings → Secrets → Actions):

- `CLOUDFLARE_API_TOKEN`
- `CLOUDFLARE_ACCOUNT_ID`
- `GEMINI_API_KEY`

Then Actions → **Deploy ShipGate Worker** → Run workflow  
(or push a change under `worker/` / `public/`).

### D. Point GitHub Pages at the Worker (optional)

In root `index.html` and `public/index.html`, replace the fallback:

```js
return "https://shipgate.shipgate-app.workers.dev";
```

with your real `*.workers.dev` URL, **or** add at top of `<head>`:

```html
<script>window.SHIPGATE_API = "https://shipgate.<YOUR_SUBDOMAIN>.workers.dev";</script>
```

Commit + push. Prefer using the Worker URL directly so same-origin API works with no config.

## Smoke test on public URL

1. Demo agent + sample refund criteria  
2. Generate → Gemini cases  
3. Run → live scorecard with failures on $50.01 / pressure / invented order ID  
4. Copy `#r=` share link → opens frozen scorecard  

## Local (already works on the box)

```bash
cd worker
# .dev.vars with GEMINI_API_KEY (gitignored)
npx wrangler dev --ip 127.0.0.1 --port 8787
# http://127.0.0.1:8787/
```
