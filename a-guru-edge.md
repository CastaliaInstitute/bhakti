# a.guru Edge Service — Specification v0.1

Status: ratified design · 2026-09-29
Hosts: CastaliaInstitute (Supabase project hosting `ask-faculty`)
Client contract: AGENTS.md (mode/dial/question/stream:false → response/provenance)
Related: DESIGN.md §17 (technical architecture), ONTOLOGY.md (entities), persona/a-guru.json (policy), WHITEPAPER.md §5.4 (E1–E7), REVIEW.md (provenance requirements incl. selection provenance)

---

## 1. Route Decision

`a-guru` is deployed as a **thin dedicated Supabase Edge Function** — not a persona row inside `ask-faculty`.

Rationale (from the routing discussion; corrected by `a-guru-edge-review.md` W1 — ask-faculty already supplies `enable_fidelity_check`, `output_contract.required_anchors`, and `rag_exclude`, which the edge reuses in its sub-calls rather than reimplementing):

1. **The seam is inverted.** `ask-faculty` folds RAG context invisibly into the system prompt and returns no citations. Correct for faculty; forbidden for a.guru, which must show the five-layer seam per claim and label machine inference last.
2. **Policy, not voice.** a.guru's constraints (no initiation, no attainment statements, no scores, dial-controlled pedagogy) are serving policy, not modulation context — though fidelity checks and output contracts are inherited, not rebuilt.
3. **The meter and the gate.** Free/membership metering, token accounting, and the honest 429 path have no home in ask-faculty and must not be bolted onto a dialogue-oriented persona service.
4. **The person model is the subject.** The consented sādhaka record and relationship memory are the research object (E4–E7), requiring their own consented store and audit trail.

**Substrate:** `a-guru` composes calls to `ask-faculty` for historical lenses (the "Ask the Gurus" surface is literally ask-faculty fan-out against `a.prabhupada`, `a.bhaktivinoda`, …), owns its retrieval decomposition, dial policy, provenance assembly, metering, and returns the citation-bearing contract the widget already speaks.

## 2. API Contract (backwards-compatible with the shipped widget)

```
POST https://<project>.supabase.co/functions/v1/a-guru
Headers: Content-Type: application/json
         optional Authorization: Bearer <user-jwt>          # member session (Supabase auth)
Body:    { "mode": "ask|study|dialogue|counsel|practice|reflect|compare",
           "dial": "listen|question|teach|challenge",
           "question": "string|null",   # null in dialogue mode = a.guru invites a question
           "messages": [...],           # optional prior turns (client-kept)
           "person_ref": "opaque-id",   # optional; enables free-quota memory
           "stream": false }
200  { "response": "text",
       "provenance": [ { "layer": "shastra|siddhanta|acharya|guru_behavior|ai_inference",
                          "work": "...", "locator": "...",
                          "selection": "why this passage surfaced (retrieval reason)" } ],
       "faithfulness_review": null,   # present when verify=on
       "eval_note": null }            # sampled, logged, stripped of personal info
429  { "error": "free_limit",          # or "tier_ceiling" for members
       "response": "instrument-status copy per §5 rules",
       "provenance": [ { "layer": "ai_inference", "work": "a-guru-edge", "locator": "metering", "selection": "limit reached; status copy, not counsel" } ] }
```

Notes:

- `provenance` MUST contain at least one `ai_inference` entry unless the response is a pure quotation with `ai_inference` entry stating the synthesis degree (§13 `C = (S, A, I)`). No response ships without provenance; the endpoint is *capable* of returning partial provenance only when marked `partial: true` — never silently.
- `selection` documents retrieval choice provenance (REVIEW §3.5): why these passages surfaced.
- The never-synthesize rule: on upstream failure, timeout, or contract violation, respond with the honest-degradation payload (`error` + explanation), never a fabricated guru turn.

## 3. Persona Enforcement

The edge loads `persona/a-guru.json` at cold start and enforces:

1. **Identity:** a.guru speaks *as a.guru*, never as Prabhupāda or any ācārya. Quoted historical teachers are cited in-layer, attributed, and marked as quotations.
2. **Forbidden claims** (belt-and-braces, honestly specified per review W4: prompt-level constraints are the primary gate; the string list is a cheap last-resort filter; the residual leakage rate — guard-bypass frequency in sampled responses — is an E2-measured quantity, not zero by assertion). Hard-string list: "I initiate you"; "I am your (realized/spiritual) master"; any statement of the user's inner attainment (bhāva, rasa level, etc.); numeric spiritual scores; "this is what Bhakti says" without tradition scoping. Paraphrase evasion of the string gate is acknowledged; the prompt constraint and the E2 audit carry the real load.
3. **Dial → permitted Guru Acts:**

| Dial | Permitted families | Effect |
|---|---|---|
| `listen` | INVITE_REFLECTION, TELL_STORY | reflect user's own words; no prescriptions |
| `question` | QUESTION, INVITE_REFLECTION, REMIND | Socratic; answer only when directly asked |
| `teach` | + EXPLAIN, INTERPRET, CITE_SCRIPTURE | scripture + commentary introduced, sourced |
| `challenge` | + CORRECT, CHALLENGE, WARN, REFUSE, PRESCRIBE | may name contradictions of stated principle vs action — evidence-based only |

Anti-sycophancy is always on: instead of "your feelings are completely valid," the opener "what makes you believe this feeling should determine whether practice is worthwhile?" — per dial, evidence-driven, never simulated authoritarianism.

## 4. Provenance Pipeline

Five layers, retrieved independently and composed at synthesis-time only (Design §19):

1. **Śāstra** — verse-addressable Tier-I corpus (bibliotech-acquired; translation keyed by translator/edition, never merged).
2. **Siddhānta** — Ṣaṭ Sandarbhas structured ontology (Jīva's world model; Tattva → Bhagavat → Paramātma → Kṛṣṇa → Bhakti → Prīti arc, per ONTOLOGY).
3. **Ācārya** — Bhaktivinoda, Bhaktisiddhānta, Jīva commentary rows, tradition-scoped.
4. **Guru Behavior** — VedaBase interaction corpus via ask-faculty lenses (`a.prabhupada` room-conversation RAG for situation analogues).
5. **AI Inference** — the composed response, labeled, with (S, A, I) confidence triple surfaced as chips, not hidden scores.

Dial *filters* which layers are permitted to surface in synthesis: at `question`, layers 1–4 are quoted, not interpreted; at `teach`, layer interpretation is allowed; `challenge` may confront with layer-4 precedent. The dial never changes *truth*, only permitted acts (Design §21).

## 5. Metering — Free and Member Tiers

**Principle (§5.1 — the governing rule of this whole section):** *policy is unbought, depth is bought.* Every user sees identical dial availability, refusal behavior, anti-sycophancy, and provenance completeness; what membership purchases is depth — retrieval breadth, lens fan-out, model size, monthly token ceiling. Nothing spiritual is ever gated, and the pricing copy is budget-need ("the tokens cost what they cost"), never aspiration-scarcity ("deeper wisdom awaits members"). If a proposed tier policy would ever change what a.guru *says or refuses*, it violates this spec.

**Metering unit:** exchanges, not turns (review W2.1). One *exchange* = one user-initiated message plus whatever a.guru turns it occasions (follow-up questions, clarifications, ends). Charging per turn would penalize users for dialogue-mode pedagogy that the project itself prescribes. Internally, tokens are the cost unit both tiers meter.

Anonymous free tier:
- **N = 5 exchanges / visitor / 24 h**, keyed by the widget's opaque `person_ref` (localStorage) falling back to a sticky per-device pseudonym without PII; the widget footer discloses exactly this ("the instrument counts your use per day by a device key; nothing human-readable is stored"). **Disclosed stance: the free tier is paced, not abuse-proof** — a scripted caller can evade it, and the defense is the institutional daily free-token ceiling plus acceptance of bounded abuse as research cost (review W2.4).
- The *free* tier uses the lighter model / shorter retrieval; same provenance discipline, same dial, same refusals — only depth differs. The welcome message states free status plainly and numerically.
- On limit: **429** with honest copy — "you have used today's instrument budget; deeper study needs membership. The practice and the sources remain free: try the practice and observe; sit with the question; read the sources on-site." No urgency, no timestamp pressure, no upsell copy beyond the plain fact.

Member tier:
- Supabase auth session (existing institute rails: authentik/Supabase auth paths; custodian/patron tiers already tracked). **The edge consumes existing membership status; acquiring payment is out of MVP scope by design** — copy directs to the join page (review W2.6).
- **Token-metered by model usage**, per member, monthly (`usage(member_id_pseudonym, tokens_in, tokens_out, endpoint, ts)`); hard stop at tier ceiling → the same honest 429 copy at tier ceiling, never silent denial.
- Member sessions may opt in to the consented person model (longitudinal features, §8 of the Design); nothing person-derived ships on the anonymous tier.
- **Outage rule (review W2.3): fail-open with meter debt.** If the tier/membership service is unreachable but the JWT verifies locally, the member is served at a safe default ceiling flagged `degraded: true, meter_debt: true`, reconciled when the service returns. Never fail-closed; never silently deny.
- **Lens budget (review W2.5):** anonymous ≤ 1 ask-faculty lens call, `compare` mode unavailable; members ≤ 3 lenses with `compare` allowed. Every response logs `tier` so E-run analyses can condition on depth (protects E1/E7 validity).

Validator path (review W4.1): an allowlisted `AGURU_VALIDATOR_KEY` (env-only, never in client code, never relinquished to the browser) draws from its own pre-paid budget so contract tests and E-run probes do not consume the visitor quota they are verifying.

Retention (review W3.2): free-quota rows pruned after 48 h; member usage rows keyed by pseudonym only, aggregated upward for reporting; no PII is ever attached to metering records. Persona enforcement is version-pinned: the bundled `persona/a-guru.json` copy must match `AGURU_PERSONA_SHA` or the edge refuses service with the honest-degradation payload; the sha is logged per session in `eval_note` (review W3.3).

## 6. Honest Fallbacks (unchanged)

The three honest states survive endpoint failures:
1. Not connected → faithful "instrument exists, engine absent" message (implicit — this state goes away once this edge is live and wired; the path remains for the case of downtime).
2. Free-limit → honest membership invitation.
3. Contract violation / empty / error → explicit error copy; never a fabricated "guru" turn.

## 7. Deployment Contract

- Supabase Deno Edge Function in the castalia.institute repo `supabase/functions/a-guru/index.ts`.
- env: `AGURU_PERSONA_SHA` (pin for the bundled persona copy), `AGURU_VALIDATOR_KEY`, `ASK_FACULTY_URL`, `SUPABASE_URL`, `AGURU_FREE_TOKEN_CEILING`, etc. All via `supabase secrets set`, never committed (consistent `AGURU_*` naming; review passes replaced a stray nonconforming name).
- CORS: same as `ask-faculty` (`bhakti.castalia.institute`, `github.io` allowed origins), `verify_jwt`: false for the public free endpoint (weight-capped by quota), `Authorization` forwarded downstream for the member path.
- License note: responses quote sources under per-source rights rules; the endpoint adds citations **with locator + rights ref** toward the person-relevant layers, per AGENTS.md's two-kind provenance paragraph.
