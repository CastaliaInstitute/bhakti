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

## 2026-09-29 — Revision pass 2 (second-pass review of the revised widget; declared fetch-stub)

Method: live page; fetch stubbed for one POST to capture the request body and
return the contract-shaped 429. Validates the *client contract* only.

Results — pass:

- `person_ref` generated (persistent UUID in localStorage) and confirmed
  **present in the POST body**, matching the quota-key design; footer carries
  the device-key disclosure sentence.
- 429 continues to render as `msg status` with provenance/selection chips.
- Welcome's engine-status chip appears only on the empty-default endpoint
  (conditional), per S2.
- In-flight submit guard present (double-Enter cannot fire two posts; not
  forced-failure-tested — asserted by code review + parse check).

Open items unchanged: edge implementation and quota enforcement (checkpoints 2–5).

## 2026-10-11 — revised live backend and isolated freemium gate

Protocol: Deno type-check all changed functions; confirm `a.guru` exists in the
faculty table; send a live `teach` request through the deployed edge; query the
feature limits read-only; exercise the anonymous bucket with a throwaway key;
then exercise the deployed public path until it returns its limit response.

Results:

- `deno check` passed for `a-guru`, `_shared/gate.ts`, and `ask-faculty`.
- `a.guru` synthetic faculty row present (`persona.metadata.version = 0.1.0`).
- Live edge POST returned HTTP 200. Response is authored by the synthetic
  `a.guru` faculty and includes a deterministic source note, separate
  "Historical comparison (unlocated)" and "a.guru inference" sections, and
  response-level `guru_behavior` + `ai_inference` provenance. The exact
  historical retrieval locators remain unavailable; cited text is explicitly
  not represented as verified.
- Membership SQL returned registered daily cap = 5 and member cap = unlimited
  (null). There is no separate token ledger.
- The isolated `a_guru_chat` bucket is independent of
  `PUBLIC_FACULTY_CHAT_DISABLE_LIMIT`; the temporary direct-RPC bucket test
  returned allowed/allowed/blocked at cap 2 and was deleted after the test.
- The deployed public edge returned HTTP 429 after the daily cap was consumed
  from this test network. The test used earlier requests in the same UTC bucket,
  so it was not a clean five-call-from-zero count. The browser verification of
  the 429 UI path follows below.
- Magic-link UI is deployed in source; the email round-trip and a real member
  JWT session were not exercised in this run.
