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

### Live Pages/browser check (2026-10-11)

Headless Chromium against `https://bhakti.castalia.institute/` after the Pages
build:

- Landing page returned 200; Supabase UMD client and public config loaded;
  `authClient.createClient` initialized; opaque `person_ref` present.
- Member sign-in control opens the email field; no email was sent in the test.
- An anonymous request at the exhausted test-network quota rendered the actual
  gate's HTTP 429 as `msg status`, with the public-limit copy and a safe,
  validated `https://castalia.institute/join` link. No engine-error message or
  generated guru answer appeared. Only console error was the expected 429.
- Screenshot: `live-chat-revision-2026-10-11.png`.

Limit: sign-in email round-trip, an active-member JWT, and member entitlement
behavior were not exercised; these remain pending. This is real anonymous
quota-path validation, not a fetch-stub simulation.

### 2026-10-11 — session context and member-limit UI contract (declared fetch stub)

On the live Pages origin, the client SDK/config loaded, the Member sign-in
control opened the email form, and a controlled `fetch` stub exercised two
successful turns followed by a 403 `upgrade_required` payload.

Results — pass:

- Second POST included the first user/assistant pair (`messages.length = 2`);
  requests also carried the persisted opaque `person_ref`.
- 403 with `upgrade_required: true` rendered as `msg status`, not engine error.
- Join link rendered and was constrained to HTTPS under `castalia.institute`.
- No page errors; no email was sent.

This is a declared client-contract simulation, not a member-server/auth test.
The magic-link round trip and active-member JWT remain untested. See
`member-limit-widget-validation-2026-10-11.png`.

### Final revalidation after copy/auth updates (2026-10-11)

- Live-page Chromium: site 200; Supabase public config + SDK initialized;
  `person_ref` created; Member sign-in toggle opens the email form; anonymous
  `listen` request returns the deterministic reflection path with AI-inference
  provenance and no console errors. This browser's IP bucket was not exhausted
  at that run, so it did not hit the server gate.
- Live edge shell request from the exhausted test-network bucket: HTTP 429 with
  the revised no-pressure copy ("You've used today's free a.guru exchanges…")
  and `https://castalia.institute/join`.
- The 403 member-limit UI path is the fetch-stub test recorded above; an actual
  active-member JWT/email round-trip remains untested.

Disclosure correction (2026-10-11): the visible footer now states that the daily
quota uses a **server-side network-address hash** and that no name/email is in
the quota key. Earlier 2026-09-29 entries use the prior, less-accurate phrase
"device key" and are historical records of that earlier UI revision.

Final page-state screenshot: `live-chat-final-2026-10-11.png` — real live
listen response (not a stub), sign-in form open, revised network-address-hash
privacy disclosure visible. HTTP 200; Supabase client loaded; no console errors.
