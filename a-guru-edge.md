# a.guru Edge Service — Specification v0.1

Status: ratified design · 2026-09-29
Hosts: CastaliaInstitute (Supabase project hosting `ask-faculty`)
Client contract: AGENTS.md (mode/dial/question/stream:false → response/provenance)
Related: DESIGN.md §17 (technical architecture), ONTOLOGY.md (entities), persona/a-guru.json (policy), WHITEPAPER.md §5.4 (E1–E7), REVIEW.md (provenance requirements incl. selection provenance)

---

## 1. Route Decision

`a-guru` is a thin dedicated Supabase Edge Function **and** a synthetic faculty persona row (`a.guru`) served through `ask-faculty` for its authored synthesis. The historical lens is a separate call; its output is context for a.guru, never passed through as the final answer.

Rationale (from the routing discussion; corrected by `a-guru-edge-review.md` W1 — ask-faculty already supplies `enable_fidelity_check`, `output_contract.required_anchors`, and `rag_exclude`, which the edge reuses in its sub-calls rather than reimplementing):

1. **The seam is inverted.** `ask-faculty` folds RAG context invisibly into the system prompt and returns no citations. Correct for faculty; forbidden for a.guru, which must show the five-layer seam per claim and label machine inference last.
2. **Policy, not voice.** a.guru's constraints (no initiation, no attainment statements, no scores, dial-controlled pedagogy) are serving policy, not modulation context — though fidelity checks and output contracts are inherited, not rebuilt.
3. **The meter and the gate.** Free/membership metering, token accounting, and the honest 429 path have no home in ask-faculty and must not be bolted onto a dialogue-oriented persona service.
4. **The person model is the subject.** The consented sādhaka record and relationship memory are the research object (E4–E7), requiring their own consented store and audit trail.

**Substrate:** `a-guru` calls `ask-faculty` once for a historical comparison lens (`a.prabhupada` in v0.1), then once as the synthetic `a.guru` faculty to write an independent answer. It owns dial policy, isolated metering, provenance assembly, and the widget contract. The first response is explicitly unlocated historical comparison material; until ask-faculty returns retrieval locators, it is not represented as a verified quotation.

## 2. API Contract (backwards-compatible with the shipped widget)

```
POST https://<project>.supabase.co/functions/v1/a-guru
Headers: Content-Type: application/json
         optional Authorization: Bearer <user-jwt>          # member session (Supabase auth)
Body:    { "mode": "ask|study|dialogue|counsel|practice|reflect|compare",
           "dial": "listen|question|teach|challenge",
           "question": "string|null",   # null in dialogue mode = a.guru invites a question
           "messages": [...],           # optional prior turns, held in this tab only
           "person_ref": "opaque-id",   # optional continuity key; not quota identity
           "stream": false }
 200  { "response": "text",
       "provenance": [ { "layer": "shastra|siddhanta|acharya|guru_behavior|ai_inference",
                          "work": "...", "locator": "...",
                          "selection": "why this passage surfaced (retrieval reason)" } ],
       "provenance_scope": "response-level; exact passage locators may be unavailable",
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

**Principle (§5.1 — target rule):** *policy is unbought, depth is bought.* Every user sees identical dial availability, refusal behavior, anti-sycophancy, and provenance completeness. The current v0.1 implementation does not vary model or retrieval depth by tier; membership currently removes the free/registered daily cap only. Any later depth-based tiering must not change what a.guru says or refuses.

**Quota unit:** one request/exchange, not generated turn count. v0.1 makes at most one a.guru response to each user request; it does not yet conduct multi-turn sessions inside one request. Actual token usage is not currently recorded in a separate ledger.

Anonymous free tier:
- **N = 5 exchanges / visitor / UTC day** on the dedicated `a_guru_chat` feature. Identity is an IP-derived server-side hash in `consume_public_edge_chat`; the widget's `person_ref` is not the quota key. The shared `PUBLIC_FACULTY_CHAT_DISABLE_LIMIT` switch does not disable this isolated quota; only the separate `AGURU_PUBLIC_CHAT_DISABLE_LIMIT` emergency switch can do so. **Disclosed stance: the free tier is paced, not abuse-proof** — rotating IPs/devices can evade it, and an institutional daily token budget caps exposure.
- The free tier uses the same model and one historical lens as registered/member chat. Its only current difference is the daily cap. The public key is server-side and responses include a join URL on limit.
- On limit: **429** with the access-gate's response and join URL; the widget renders it as an instrument status, not an answer or an engine error. Copy should preserve non-sales alternatives (practice, sit with the question, use public sources).

Member tier:
- Supabase auth session (existing Supabase entitlement tiers). Registered accounts receive the `a_guru_chat` registered-tier cap (5/day); active members currently have no daily cap via the existing tier function. **There is no separate member-token ledger yet**, and the client sign-in flow forwards Supabase JWTs but does not settle payment itself.
- Member sessions may opt in to the consented person model (longitudinal features, §8 of the Design); nothing person-derived ships on the anonymous tier.
- **Outage behavior:** not yet independently verified for a.guru's entitlement call; test before claiming fail-open with meter debt. Anonymous RPC errors currently fail open in the shared gate (see `_shared/gate.ts`).
- **Lens budget (review W2.5):** anonymous ≤ 1 ask-faculty lens call, `compare` mode unavailable; members ≤ 3 lenses with `compare` allowed. Every response logs `tier` so E-run analyses can condition on depth (protects E1/E7 validity).

Access implementation note (2026-10-11): public traffic uses the dedicated `a_guru_chat` hashed-IP bucket; signed-in traffic uses `a_guru_chat` in `use_feature`/`chat_feature_daily_limit`. The a.guru edge never toggles the institute-wide faculty-chat bypass.

Validator path: no validator key is implemented yet. Contract tests use a separate controlled bucket where possible; live quota tests consume the test network's daily allowance.

Retention: the public quota table stores an IP-derived hash and UTC-day counter; an a.guru-specific cleanup/retention job has not yet been verified. Persona enforcement is version-pinned: the bundled `persona.json` is imported into the edge and its canonical JSON SHA-256 is compared to `AGURU_PERSONA_SHA`; mismatch refuses service; the sha prefix is logged per session.

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
