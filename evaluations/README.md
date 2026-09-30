# Evaluations

Run configurations, results, and evidence for the pre-registered evaluations
(WHITEPAPER.md §5.4). External pre-registration pending.

## 2026-09-29 — Chat interface validation ( conducted: automated browser)

Protocol: open https://bhakti.castalia.institute/ in a controlled Chrome
instance; verify widget structure (A11y tree), open the panel, submit a
message, exercise the Guru Dial, submit an empty message, inspect console.

Results — all pass:

- Panel opens/closes; aria-expanded toggles are correct; log is aria-live=polite.
- Welcome message states the boundary ("experimental guru-function, not a guru")
  and renders provenance chips; footer carries the no-initiation/no-score line.
- POST to `/api/a-guru` (GitHub Pages cannot answer POST) returns **405**;
  the widget degrades to the honest "no engine attached" message with the
  `localStorage.aguru.endpoint` override instructions — no synthesized guru
  content at any point. Console shows only the expected 405s.
- Guru Dial: 4 stops verified (`listen`/`question`/`teach`/`challenge`), label
  updates on input.
- Empty submit correctly renders the "(invite a question)" user turn.

Endpoint state for validation: default `/api/a-guru` (engine not yet attached).

Evidence: `chat-validation-2026-09-29.png` (sidebar open, question + fallback
message + dial visible).

## 2026-09-29 — 429 `needs_membership` rendering (UI path; declared simulation)

Method: live page fetch; the widget's `fetch` was stubbed for exactly one call
to return the contract-shaped 429 body (a-guru-edge.md §2W5). This validates
the *rendering path* only — the real edge does not exist yet; no live server
result is claimed.

Results — pass:

- Status `429` renders as `msg status` ("instrument status · not counsel"),
  never as the engine-error genre ("engine did not respond").
- Copy is the honest no-upsell variant with the three non-sales alternatives.
- Selection provenance chips render on status turns per the payload contract.
- Screenshot: `429-status-validation-2026-09-29.png`.

Also revised this date: metering unit fixed to *exchanges* (one user message
plus a.guru's occasioned turns — never per-turn), policy never tiered (a-guru
-edge-review.md W1/W2.2), member fail-open with meter debt on outage, lens
budgets per tier, persona sha pin, validator key plan.
