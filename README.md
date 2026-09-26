# ShipGate

**Paste English AI-agent acceptance criteria → auto-generate eval checks → (simulated) run → shareable pass/fail scorecard.**

Solo MVP · Week 1 · Adaptive6 nights/weekend optionality. Client-side only; no backend.

## Try it

Open `index.html` in a browser, or serve the folder:

```bash
cd shipgate && python3 -m http.server 8080
# then visit http://localhost:8080/
```

1. Edit (or keep) the acceptance criteria  
2. **Generate eval suite** → **Run against agent**  
3. Copy the **shareable scorecard** link — anyone with the link sees the same frozen result (state lives in the URL hash; no server)

## Share links

After a run, the address bar becomes something like:

`…/index.html#r=<base64url-json>`

Opening that URL restores the scorecard (pass counts, failures, agent label). Starting a new run clears the hash.

## Limits (Week 1)

- Agent “runs” are **simulated** (demo refund / RAG narratives). Custom HTTPS URLs are accepted but not called.
- No live LLM judge, no auth, no persistence beyond the share hash.
- Hash payloads can get long if criteria are huge; keep criteria concise for shareable links.

## Deploy (parent)

Static host of this folder is enough (GitHub Pages or Vercel static). Do not require a build step.
